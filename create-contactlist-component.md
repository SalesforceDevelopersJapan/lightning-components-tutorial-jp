---
layout: module
title: Module 4&#58; Creating the ContactList Component
---

In this module, you create a Lightning Component responsible for displaying the list of contacts and you add the ContactList component to the QuickContacts component.

## What you will learn
- Use component attributes
- Use event handlers
- Use a Lightning Component in another Lightning Component


## Step 1: Create the Component

1. In the Developer Console, click **File** > **New** > **Lightning Component**. Specify **ContactList** as the bundle name and click **Submit**

2. Implement the component as follows:

    ```
    <aura:component controller="ContactListController">

        <aura:attribute name="contacts" type="Contact[]"/>
        <aura:handler name="init" value="{!this}" action="{!c.doInit}" />

        <ul>
            <aura:iteration items="{!v.contacts}" var="contact">
                <li>
                    <a href="{! '#/sObject/' + contact.Id + '/view'}">
                        <p>{!contact.Name}</p>
                        <p>{!contact.Phone}</p>
                    </a>
                </li>
            </aura:iteration>
        </ul>

    </aura:component>
    ```

    ### Code Highlights:
    - The controller assigned to the component (first line of code) refers to the **server-side controller** (ContactListController) you created in module 2.
    - The **contacts** attribute is defined to hold the list of Contact objects returned from the server.
    - The **init** handler is defined to execute some code when the component is initialized. That code (**doInit**) is defined in the component's
**client-side controller** (you'll implement the controller in the next step).
    - ```<aura:iteration>``` is used to iterate through the list of contacts and create an ```<li>``` for each contact


1. Click **File** > **Save** to save the file.


## Step 2: Implement the Controller

1. Click **CONTROLLER**

    ![](images/component-controller.jpg)

1. Implement the Controller as follows:

    ```
    ({
        doInit : function(component, event) {
            var action = component.get("c.findAll");
            action.setCallback(this, function(a) {
                component.set("v.contacts", a.getReturnValue());
            });
            $A.enqueueAction(action);
        }
    })
    ```

    ### Code Highlights:
    - The controller has a single function called **doInit**. This is the function the component calls when it is initialized.
    - You first get a reference to the **findAll()** method in the component's server-side controller (ContactListController), and store it in the **action** variable.
    - Since the call to the server's findAll() method is asynchronous, you then register a callback function that is executed when the call returns. In the callback function, you simply assign the list of contacts to the component's **contacts** attribute.
    - $A.enqueueAction(action) sends the request the server. More precisely, it adds the call to the queue of asynchronous server calls. That queue is an optimization feature of Lightning.

1. Click **File** > **Save** to save the file


## Step 3: Add ContactList to the QuickContacts Component

1. In the developer console, go back to the **QuickContacts** component.

    If you don't see the tab in the developer console, click **File** > **Open Lightning Resources** in the Developer Console menu, select **QuickContacts** > **COMPONENT** in the dialog, and click the **Open Selected** button.

    ![](images/lightning-resources.jpg)


1. Replace the ContactList placeholder with the actual component:

    ```
    <aura:component implements="force:appHostable">

        <c:ContactList/>

    </aura:component>
    ```

    > **c:** is the default namespace for Lightning components

1. Click **File** > **Save** to save the file

1. Go back to the Salesforce1 app and reload **Quick Contacts** from the menu to see the changes:

    ![](images/version2.jpg)


## Step 4: Style the ContactList Component

In this step, you'll add some CSS styles to the component to make it look like a standard mobile list. You'll remove the item bullets, add a horizontal line between items, and fine tune margins.


1. Click **STYLE**

    ![](images/component-style.jpg)

1. Implement the following styles:

    ```
    .THIS {
        list-style-type: none;
        padding: 0;
        margin: 0;
    }

    .THIS li {
        border-bottom: solid 1px #DDDDDD;
        padding: 8px;
    }

    .THIS p {
        margin: 4px;
    }
    ```

1. Click **File** > **Save** to save the file

1. Go back to the Salesforce1 app and reload **Quick Contacts** from the menu to see the changes:

    ![](images/version3.jpg)



<div class="row" style="margin-top:40px;">
<div class="col-sm-12">
<a href="create-lightning-application.html" class="btn btn-default"><i class="glyphicon glyphicon-chevron-left"></i> Previous</a>
<a href="create-searchbar-component.html" class="btn btn-default pull-right">Next <i class="glyphicon glyphicon-chevron-right"></i></a>
</div>
</div>
