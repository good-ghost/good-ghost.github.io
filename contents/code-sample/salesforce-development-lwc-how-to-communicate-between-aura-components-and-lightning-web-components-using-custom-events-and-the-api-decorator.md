---
title: "Salesforce Development (LWC): How to Communicate between Aura Components and Lightning Web Components Using Custom Events and the Api Decorator"
sort: 2
---

[source from](https://codingwiththeforce.com/salesforce-development-lwc-how-to-communicate-between-aura-components-and-lightning-web-components-using-custom-events-and-the-api-decorator/)

# Salesforce Development (LWC): How to Communicate between Aura Components and Lightning Web Components Using Custom Events and the Api Decorator

## Why Would You Want to Communicate Between Component Types?

There are a bunch of reasons it’s beneficial to communicate between component types but the most common ones that I have found are the following:

1) You’re building a new component and realize you need functionality that only Aura Components support, but it’s a very small piece of the component. Instead of building the entire thing in an outdated and much less performant Aura Component, you build most of it in an LWC and just communicate to a small Aura component to do the function only it can do.

2) You’re staffed in an Aura heavy org and when you start building new things for the existing Aura components you should build them in LWC. You can easily do this, but you need to know how the communication channels work between component types.

Once you see how easy it is to communicate between components it’ll be much easier to switch to LWC’s to do development for everything moving forward.

## How to Communicate from an LWC to an Aura Component

This is super simple but it’s confusing if you’ve never done it before. We’re basically just gonna create a [custom event in our LWC](https://developer.salesforce.com/docs/component-library/documentation/en/lwc/lwc.events_create_dispatch) and handle that event in the Aura Component. Let’s check out the code and then I’ll explain it a bit:

``` html
<!--This is the LWC HTML-->
<template>
    <lightning-input class="dataToSend" placeholder="Enter text to transfer">
    </lightning-input>
    <lightning-button variant="brand" label="Pass data to Aura Component" 
    onclick={communicateToAura} ></lightning-button>
</template>
```

``` javascript
//This is the LWC JS Controller
import {LightningElement} from 'lwc';

export default class LwcCommunication extends LightningElement
{
    //This method creates a custom event that dispatches itself.
    //The Aura component then handles this event
    communicateToAura()
    {
         console.log('Communicating to Aura ::: ');

         //We are grabbing the value from the lightning input field that has the dataToSend 
         //class
         let dataToSend = this.template.querySelector(".dataToSend").value;

        //We are creating a custom event named senddata and passing a value in the detail 
        //portion of the custom event
        const sendDataEvent = new CustomEvent('senddata', {
            detail: {dataToSend}
        });

        //Actually dispatching the event that we created above.
        this.dispatchEvent(sendDataEvent);
    }
}
```

``` html
<!--This is the Aura Component HTML-->
<aura:component description="Aura_Communication">
	<aura:attribute name="dataReceived" type="String"/>

	<!--The onsenddata is what handles the custom event we made in our LWC-->
	<c:lwc_Communication onsenddata="{!c.receiveLWCData}" aura:id="lwcComp" />
        <p>This is the data receieved from our LWC: {!v.dataReceived}</p>
</aura:component>
```

``` javascript
({
       //This is the Aura JS Controller
	
        //This method receives data from our LWC and sets the dataReceived
	//Aura attribute with the events dataToSend parameter (this is the name of the 
        //variable we send in the LWC)
	receiveLWCData : function(component, event, helper)
	{
	    component.set("v.dataReceived", event.getParam("dataToSend"));
	}
});
```

Alright so now that you’ve checked out the code let’s go over this just a lil bit. In the communicateToAura method above you can see that we create a CustomEvent object and we give it the name ‘senddata’, we also pass some data in the custom event by using the detail property of our Javascript custom event object.

Then in the Aura component’s html we can see that we import our lightning web component using this line:

``` html
<c:lwc_Communication onsenddata="{!c.receiveLWCData}" aura:id="lwcComp" />
```

You can see that when we import our lightning web component that we have an onsenddata event that calls a method in the Aura Components javascript controller called receiveLWCData. When you dispatch your senddata event in your lightning web component the Aura component handles it with the onsenddata event attached to the lightning web component.

Finally you can see that we get the data from the event that was passed to us in the receieveLWCData in the Aura Components JS controller. The most important part of the method is the [event.getParam(“dataToSend”)](https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lightning/lightning_base_events.htm). You are grabbing the variable that you passed into the detail property of your CustomEvent object in your LWC. Let me put them side by side so you see exactly what I mean:

``` javascript
//LWC Custom Event code
const sendDataEvent = new CustomEvent('senddata', {
            detail: {dataToSend}
});

//Aura code
component.set("v.dataReceived", event.getParam("dataToSend"));
```

And believe it or not it’s really that simple. You have successfully passed data from your LWC to your Aura component. Now let’s figure out how to do this in reverse.

## How to Communicate from an Aura Component to an LWC

Alright so how do we do the exact opposite of what we did above?? It’s pretty simple but we need to leverage the wonderful @api decorator for lightning web components to make this work as opposed to Aura [event communication](https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lightning/events_intro.htm). Alright so let’s check out the code below and then I’ll go over it a bit more in detail:

``` html
<!--This is the LWC Template/HTML-->
<template>
	<p>This is the data received from the Aura Component: {dataReceived}</p>
</template>
```

``` javascript
//This is the LWC JS Controller
import {LightningElement, api, track} from 'lwc';

export default class LwcCommunication extends LightningElement
{
	//Tracked variables ensure that they are refreshed on the page when their values are
	//updated in the code
	@track dataReceived;

	//The api decorator makes this a public method that any component that houses this 
        //component can access/call
	@api receiveData(data)
	{
	    this.dataReceived = data;
	}
}
```

``` html
<!--Aura Component HTML-->
<aura:component description="Aura_Communication">

	<!--The onsenddata is what handles the custom event we made in our LWC-->
	<c:lwc_Communication aura:id="lwcComp" />

	<lightning:input aura:id="dataToPass" />

	<lightning:button label="Pass data to LWC" onclick="{!c.passDataToLWC}"/>
</aura:component>
```

``` javascript
({
        //This is the Aura Component JS Controller

	//This method sends out data to our LWC from the Aura component.
	passDataToLWC : function(component, event, helper)
	{
		let stringToSend = component.find("dataToPass").get("v.value");

		//We are calling the receieveData method in our Lightning Web Component here
		component.find("lwcComp").receiveData(stringToSend);
	}
});
```

Alright, so as you can see nothing super complicated just some weird stuff you may not be super comfortable with yet. There are two very important pieces to making the passage of data between components possible.

The first is the @api method in the LWC. The receiveData method has the @api decorator in front of it. This makes the method public available to its parent component regardless of what component type it is.

The second is the component.find(“lwcComp”).receiveData(stringToSend) line in the passDataToLWC method in the Aura Components Javascript controller. This is finding the lwc that we imported into our Aura Component by its aura:id and then calling the receiveData method in the LWC (the method with the @api decorator) and passing it data.

This is surprisingly simple thankfully, no weird hacky tricks necessary just a few lines of code and we’re all good to go! If any of this is super confusing please check out the video above. I go over every aspect of the code in great detail.

