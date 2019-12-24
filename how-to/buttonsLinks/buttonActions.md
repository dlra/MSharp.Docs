# Button Actions

## Problem

Buttons are an essential part of a user interface, designed to allow users an intuitive way of interacting with the main mechanics of the application system. To make them useful, we must register actions to be executed on click events occurring.

## Implementation

The most important method you will chain to your `ModuleButton` object is `OnClick(Action<Workflow>)`.

#### Workflow class

The `Workflow` class encapsulates many methods that codify a large amount of the processes that you will typically need to use. These include:

* Navigation and window: `Go()` (for same-window navigation), `Popup()`, `Reload()`, `ReturnToPreviousPage()`, `CloseModal`, etc;
* Control flow: `If()`, `ElseIf()`, `Else()`;
* Database: `SaveInDatabase()`, `RunInTransaction()` (transactions are covered [HERE](transactions.md)).

For atypical processes/workflows, use the `CSharp()` method and add your code as a string.

#### Example

We now integrate actions behind our button clicks in our M# module:

```csharp
using Domain;
using MSharp;

namespace Modules
{
    public class CustomerForm : FormModule<Customer>
    {
        public AgenciesEmailTemplateForm()
        {
            HeaderText("Customer Details");
            
            // Header features
            // ...

            // Setting some property values via form fields
            // ...

            //================ Buttons: ================

            Button("Cancel")
                .OnClick(x => x.ReturnToPreviousPage());
            Button("Save").IsDefault()
                .OnClick(x =>
                {
                    x.CSharp("// some custom action");
                    x.SaveInDatabase();
                });
        }
    }
}
```

The Save actions are initiated in the controller via the MVC route `CustomerForm/Save` which calls the `Save` method.
Included in this is the `// some custom action` code and the generated `SaveInDatabase()` method.

```csharp
using System;
using System.Web.Mvc;
using System.Web.Routing;
using MSharp.Framework;
using MSharp.Framework.Mvc;
using Domain;
using vm = ViewModel;

namespace Controllers
{
    public partial class CustomersEnterController : BaseController
    {
        // some controller code
        // ...

        [HttpPost, Route("CustomerForm/Save")]
        [ValidateAntiForgeryToken]
        public ActionResult Save(vm.CustomerForm info)
        {
            using (var scope = Database.CreateTransactionScope())
            {
                // some custom action
                
                try
                {
                    if (!SaveInDatabase(info)) return JsonActions(info);
                }
                catch (MSharp.Framework.ValidationException ex)
                {
                    return Notify(ex.Message, "error");
                }
                
                scope.Complete();
            }
            
            return JsonActions(info);
        }
        
        [NonAction]
        public bool SaveInDatabase(vm.CustomerForm info)
        {
            if (!ModelState.IsValid(info))
            {
                Notify(ModelState.GetErrors().ToLinesString(), "error");
                return false;
            }
            
            var item = info.Item.IsNew ? info.Item : info.Item.Clone();
            
            info.CopyDataTo(item); // Read the ViewModel data into the domain object.
            
            using (var scope = Database.CreateTransactionScope())
            {
                try
                {
                    info.Item = Database.Save(item);
                    
                    scope.Complete();
                    return true;
                }
                catch (MSharp.Framework.ValidationException ex)
                {
                    Notify(ex.ToFullMessage(), "error");
                }
                
                return false;
            }
        }
    }
}

namespace ViewModel
{
    [EscapeGCop("Auto generated code.")]
    public partial class CustomerForm : IViewModel
    {
        public Customer Item { get; set; }
    }
}
```

In the view, the MVC route is pattern-matched to call the `Save()` method in the controller.
The Cancel `Button` object is given the `CommonActivity` method `ReturnToPreviousPage()` 
in the M# module. This generates
a client-side action (coded in the Razor script via the `onclick` attribute) in the
 view but no code in the controller is required to run this.

```html
@model ViewModel.CustomerForm
<form data-module="CustomerForm" 
      role="form"
      method="post" 
      action="@Url.Current()" 
      data-validation-style="tooltip" 
      class="input-form">
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
            <button name="Cancel" 
                    class="btn btn-default" 
                    onclick="return page.returnToPreviousPage(this);" 
                    data-redirect="ajax">
                Cancel
            </button>
            <button type="submit" 
                    name="Save"
                    class="btn btn-primary" 
                    formaction='@Url.ActionWithQuery("CustomerForm/Save")' 
                    default-button="true">
                Save
            </button>
        </div>
    </div>
</form>
<!-- «End| CustomerForm» -->

```