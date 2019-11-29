# Default Button

## Problem

Sometimes it is useful to flag one of your buttons in your module as the default button. With this flag, we can easily tailor styling and actions on a specific class of button to improve the UX of our appliction

## Implementation

#### ModuleButton class

The `ModuleButton` class includes an `IsDefault()` method which can be chained on to your instantiated `ModuleButton` object.

#### Example

Let's consider our skeletal customer form again.

```csharp
using MSharp;
using Domain;

namespace Modules
{
    public class CustomerForm : FormModule<CustomerForm>
    {
        public CustomerForm()
        {
            // Header features
            // ...
            
            // Setting some Customer property values via form fields
            // ...
            
            //================ Buttons: ================
            
            Button("Cancel")
            Button("Save").IsDefault()
        }
    }
}
```

Note that the _Cancel_ button markup has not changed. The _Save_ button class attribute is now set to `.btn.btn-primary` and it has an additional attribute, `defaualt-button` which has been set to `true`. These customisations to the markup allow for specific styling and event-handling that target this button.

```html
@model ViewModel.CustomerForm
<!-- «Start| CustomerForm» -->
<form data-module="CustomerForm" role="form" method="post" action="@Url.Current()" data-validation-style="tooltip" class="input-form">
    <!-- «Start| CustomerForm» -->
    <!--
        ...
    -->
    <h2>Customer Details</h2>
    <div class="form-body">
    <!--
        ...
    -->
    </div>
    <div class="buttons-row">
        <div class="buttons">
            <button name="Cancel" class="btn btn-default">
                Cancel
            </button>
            <button name="Save" class="btn btn-primary" default-button="true">
                Save
            </button>
        </div>
    </div>
</form>
<!-- «End| CustomerForm» -->

```