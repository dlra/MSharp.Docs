# Adding a Button

## Problem

Buttons are fundamental elements of web pages. We need to add buttons to our UI to complete certain actions, be it navigating, saving, deleting&#x2014;generally, execution of some process.

## Implementation

The `Module` class contains three methods with a return type of `ModuleButton`: `Button()`, `Link()` and `Image()`.

#### Example

Let's consider constructing a customer form which allows us to capture a Customer's data. Initially, we will place the buttons in our `#UI` module.

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
            
            Button("Cancel");
            Link("Link");
            Image("Image");
            Button("Save");
        }
    }
}
```
As we have not chained any methods to customise these buttons/links, no code is added to the controller and so we simply have the Razor markup snippet in the view:

```html
<div class="buttons-row">
    <div class="buttons">
        <button name="Cancel" class="btn btn-default">Cancel</button>
        <a name="Link" href="#">Link</a>
        <button name="Image" class="btn btn-default">Image</button>
        <button name="Save" class="btn btn-default">Save</button>
    </div>
</div>
```

Note that an uncustomised `ModuleButton` instantiated from `Image()` generates a general button. Generating an `<img/>` tag in your view is discussed [here](../filesAndImages/displayImage.md).