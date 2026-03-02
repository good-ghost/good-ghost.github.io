---
title: "Communication between independent lightning web component and Aura component"
sort: 1
---

[source from](https://salesforcecodes.blogspot.com/2020/07/communication-between-independent-aura-and-lwc-component.html)

# Communication between independent lightning web component and Aura component

You can use `pubsub` library or lightning message service to communicate between components that are not in same DOM hierarchy. These methods can be used to communicate between sibling components in an outer lwc component or between to lwc components that are dropped separately in lightning app builder.

Here we will go through an example of lwcComponentA that displays three car company names using radio button. When a car company is selected in the child component we will fire a `carselected` event from child component with the name selected by the user. Parent component will listen to this event and display the selected name in parent component. Parent component `auraComponentB`, will listen to this event and display the selected name in `auraComponentB`.

## Example

1) Create a utility lwc component with name pubsub. Copy paste below code into it's JavaScript file. Please note that this is a utility component provided by Salesforce. But current version available in the link is doing pageRef check and it is not working as expected. So use below version shared by another Salesforce team member instead.

pubsub.js
``` javascript
/**
 * A basic pub-sub mechanism for sibling component communication
 *
 * TODO - adopt standard flexipage sibling communication mechanism when it's available.
 */

const events = {};

/**
 * Registers a callback for an event
 * @param {string} eventName - Name of the event to listen for.
 * @param {function} callback - Function to invoke when said event is fired.
 * @param {object} thisArg - The value to be passed as the this parameter to the callback function is bound.
 */
const registerListener = (eventName, callback, thisArg) => {

    if (!events[eventName]) {
        events[eventName] = [];
    }

    const duplicate = events[eventName].find(listener => {
        return listener.callback === callback && listener.thisArg === thisArg;
    });

    if (!duplicate) {
        events[eventName].push({ callback, thisArg });
    }
};

/**
 * Unregisters a callback for an event
 * @param {string} eventName - Name of the event to unregister from.
 * @param {function} callback - Function to unregister.
 * @param {object} thisArg - The value to be passed as the this parameter to the callback function is bound.
 */
const unregisterListener = (eventName, callback, thisArg) => {
    if (events[eventName]) {
        events[eventName] = events[eventName].filter(
            listener =>
                listener.callback !== callback || listener.thisArg !== thisArg
        );
    }
};

/**
 * Unregisters all event listeners bound to an object.
 * @param {object} thisArg - All the callbacks bound to this object will be removed.
 */
const unregisterAllListeners = thisArg => {
    Object.keys(events).forEach(eventName => {
        events[eventName] = events[eventName].filter(
            listener => listener.thisArg !== thisArg
        );
    });
};

/**
 * Fires an event to listeners.
 * @param {object} pageRef - Reference of the page that represents the event scope.
 * @param {string} eventName - Name of the event to fire.
 * @param {*} payload - Payload of the event to fire.
 */
const fireEvent = (pageRef, eventName, payload) => {
    if (events[eventName]) {
        const listeners = events[eventName];
        listeners.forEach(listener => {
            try {
                listener.callback.call(listener.thisArg, payload);
            } catch (error) {
                // fail silently
            }
        });
    }
};

export {
    registerListener,
    unregisterListener,
    unregisterAllListeners,
    fireEvent
};
```

2) Create a utility lwc component with name auraPubSub Copy paste below code into it's JavaScript file.Please note that this is a utility component provided by Salesforce.

auraPubsub.js
``` javascript
/**
 * A Lightning Web component that wraps the methods of the pubsub ES6 module.
 * This utility component allows Aura components to exchange messages (events) with Lightning web components.
 * You can import an ES6 module directly into an Aura component, but in this case, we need to get the current page reference.
 * You can't get the current page reference in an ES6 module; you must use a Lightning web component.
 * Usage: add this component to an Aura component to publish events or subscribe to events using the pubsub utility.
 * Do NOT use this component inside Lightning web components where you can import the pubsub module directly.
 * The pubsub approach is used to provide a communication mechanism between sibling components assembled in a flexipage (App Builder) where traditional parent/child communication patterns are not available.
 * Do NOT use this utility for parent/child communication.
 */
import { LightningElement, api, wire } from 'lwc';
import { CurrentPageReference } from 'lightning/navigation';
import {
    registerListener,
    unregisterListener,
    unregisterAllListeners,
    fireEvent
} from 'c/pubsub';

export default class AuraPubsub extends LightningElement {
    @wire(CurrentPageReference) pageRef;

    connectedCallback() {
        this.dispatchEvent(new CustomEvent('ready'));
    }

    @api
    registerListener(eventName, callback) {
        registerListener(eventName, callback, this);
    }

    @api
    unregisterListener(eventName, callback) {
        unregisterListener(eventName, callback, this);
    }

    @api
    unregisterAllListeners() {
        unregisterAllListeners(this);
    }

    @api
    fireEvent(eventName, data) {
        console.log('eventName :'+eventName+'data :'+data);
        fireEvent(this.pageRef, eventName, data);
    }
}
```

3) Create a lwc component with name lwcComponentA

``` css
.green-border {
    border: 3px solid green;
    padding: 50px;
    width: 250px;
    height: 250px;
    float:left;
}
```

``` html
<template>
    <div class="green-border">
        <h1>Component A (Publisher)</h1>
        <br/>
        <lightning-radio-group 
            name="cars" label="Cars" options={cars} value={selectedValue} type="radio" onchange={sendDataToComponentB}>
        </lightning-radio-group>
    </div>
</template>
```

``` javascript
import { LightningElement, wire } from 'lwc';
import { fireEvent } from 'c/pubsub';
import { CurrentPageReference } from 'lightning/navigation';

export default class LwcComponentA extends LightningElement {
  @wire(CurrentPageReference) pageRef;

  cars = [
    { label: "Audi", value: "Audi" },
    { label: "Tesla", value: "Tesla" },
    { label: "BMW", value: "BMW" },
  ];
  selectedValue = '';

  sendDataToComponentB(event) {
    fireEvent(this.pageRef ,'carselected', event.target.value);
  }
}
```

4) Create a aura component with name auraComponentB

``` html
<aura:component>
    <aura:attribute name='valueGotFromA' type='String'/>
    <c:auraPubsub aura:id="pubsub" onready="{!c.handlePubsubReady}" />
    <div class="red-border">
        <h1>Component B (Subscriber)</h1>
        <br/>
        Value obtained from component A 
        <br/>
        <b>{!v.valueGotFromA}</b>
    </div>
</aura:component>
```

``` css
.THIS.red-border {
    border: 3px solid red;
    padding: 50px;
    width: 250px;
    height: 250px;
    float:left;
    margin-left: 10px;
}
```

``` javascript
({
    handlePubsubReady: function (component) {
        console.log('reached in aura component controller');
        var pubsub = component.find('pubsub');
        var callback = $A.getCallback(function (data) {
            console.log('INSIDE');
            console.log('called', JSON.parse(JSON.stringify(data)));
            component.set('v.valueGotFromA', JSON.parse(JSON.stringify(data)));
            console.log(component.get("v.valueGotFromA"));
            // dataLayer.push(JSON.parse(JSON.stringify(data)));
            // console.log('dataLayer', dataLayer);
        });
        console.log('going to register listner', callback);
        pubsub.registerListener('carselected', callback);
        console.log('after register listner');
    },
})
```

5) Create a aura test app lwcToAuraSiblingTest in which lwcToAuraSiblingTest.App can contain both components

``` html
<aura:application extends="force:slds">
  <div>
    <c:lwcComponentA></c:lwcComponentA>
    <c:auraComponentB></c:auraComponentB>
  </div>
</aura:application>
```

6) Now if you open the aura app, it will look like

![result](/assets/images/lwc%20pubsub.png)

## Explanation

In `lwcComponentA` component, when value of car is changed in the radio button, it calls JavaScript method in controller `sendDataToComponentB`. This function uses `fireEvent` method in `pubsub` library and fires a custom event with name `carselected`. Selected carname is also passed as data in the event payload.

`auraComponentB` component, has a listener added for `carselected` event. It is achieved by using `registerListener` method in auraPubsub utility. Inside handlePubsubReady methos we add a listener using the line `registerListener('carselected', callback);`. This basically tells "whenever a carselected event is received, go to `callback` inside `handlePubsubReady` method in JavaScript controller.
