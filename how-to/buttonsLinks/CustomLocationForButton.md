# Adding a Button

## Problem

From a user-experience (UX) perspective, button placement should be consistent in your application. M# follows accepted UX conventions for button placement, based on general assumptions of typical user-attention flow: in form modules they are placed in the bottom-right region of the main view; in list modules they are placed in the top-right region of the main view.

Sometimes however, you may require atypical button placement in your module. Let's deal with this.

## Implementation

To customise a button location in your UI module, first add your `ModuleButton` (discussed [here](addingButton.md)) in your module constructor. When customising location, placement of the instantiating method is unimportant as it usually is&#x2014;you can add it to the beginning, the end or somewhere in the middle of your constructor code.

To locate the button, simply mark up the button name (which will be the button text compounded into Pascal case) using the syntax `[#BUTTONS(MyButton)#]`.

#### Example

Let's consider again the customer form. We place all buttons into our constructor, and then add the customised-location button markup where relevant.

```csharp
using Domain;
using MSharp;

namespace Modules
{
    public class CustomerForm : FormModule<Customer>
    {
        public CustomerForm()
        {
            HeaderText("Customer Details [#BUTTONS(HeaderButton)#]")
                .Footer(@"<div class='buttons'>
                            [#BUTTONS(FooterButton1)#][#BUTTONS(FooterButton2)#]
                        </div>");
            // Setting some Customer property values via form fields
            // ...

            //================ Buttons: ================

            Button("Cancel");
            Button("Header button");
            Button("Save");
            Button("Footer button 1");
            Button("Footer button 2");
        }
    }
}
```
As we have not chained any methods to customise these buttons/links, no code is added to the controller and so we simply have the Razor markup snippet in the view:

```html
<!-- «Start| CustomerForm» -->
<form data-module="CustomerForm" role="form" method="post" action="@Url.Current()" data-validation-style="tooltip" class="input-form">
    <!-- 
       ...
    -->
    <h2>Customer Details
        <button name="HeaderButton" class="btn btn-default">
            Header button
        </button>
    </h2>
    <div class="form-body">
    <!-- 
       ...
    -->
    </div>
    <div class="buttons-row">
        <div class="buttons">
            <button name="Cancel" class="btn btn-default">Cancel</button>
            <button name="Save" class="btn btn-default">Save</button>
        </div>
   </div>
   <div class='buttons'>
        <button name="FooterButton1" class="btn btn-default">
            Footer button 1
        </button>
        <button name="FooterButton2" class="btn btn-default">
            Footer button 2
        </button>
   </div>
</form>
<!-- «End| CustomerForm» -->
```

Note that the header and footer buttons have been moved out of the buttons row into their customised positions.