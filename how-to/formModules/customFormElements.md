# Custom Form Elements

## Problem

In some instances, data within your application needs to be handled in an atypical way that
doesn't fit within the confines of M#'s data piplines.

## Implementation
We consider the `CustomField()` method. It returns a `GenericFormElement` object which,
until methods are chained on to it to set its properties, is essentially a blank canvas.

Unlike with other derivations of `FormElement`, M# does not generally set default values for a `GenericFormElement` object;
therefore, it is essential to chain certain important methods on to your instantiating `CustomField()` method,
including `PropertyName()` and `Label()`. Often you will also want to chain on to your `CustomField()` the
`PropertyType()` method&#x2014;although type _does_ actually have a default (`string`) setting, however, so it's not essential.

#### Example
Let's consider a scenario where we want to have some sort of date filter on our form to allow us to intelligently fill the form in (e.g., perhaps it narrows the data source for other fields). However&#x2014;for whatever reason&#x2014;it won't be necessary to persist this filter data in our database, so we have not included the properties in our `IntelligentlyEnteredData` entity (not shown):

```csharp
using Domain;
using MSharp;

namespace Modules
{
    public class IntelligentlyEnteredDataForm : FormModule<IntelligentlyEnteredData>
    {
        public IntelligentlyEnteredDataForm()
        {
            var isVisible = "info.Automated == false";
            var weekEndingDateViewModelAttribute = "[WeekEndingDate(ErrorMessage = \"The week ending date is not valid.\")]";

            CustomField().Label("Week ending date from")
                .Mandatory()
                .PropertyName("WeekEndingDateFrom")
                .ControlMarkup("@Html.CustomDatePickerFor(x => x.WeekEndingDateFrom)")
                .PropertyType("DateTime?")
                .RequiredValidationMessage("The Week ending date from field is required.")
                .ViewModelAttributes(weekEndingDateViewModelAttribute)
                .VisibleIf(isVisible);
            CustomField().Label("Week ending date to")
                .Mandatory()
                .PropertyName("WeekEndingDateTo")
                .ControlMarkup("@Html.CustomDatePickerFor(x => x.WeekEndingDateTo)")
                .PropertyType("DateTime?")
                .RequiredValidationMessage("The Week ending date to field is required.")
                .ViewModelAttributes(weekEndingDateViewModelAttribute)
                .VisibleIf(isVisible);

                // some UI features which intelligently use these date filters
                // ...
            });
        }
    }
}
```

Note that we can, if it might make things clearer, set some string variables here at the top of the constructor as a minor bit of code re-use.

We generate the following controller:

```csharp
using System;
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;
using System.Web.Mvc;
using MSharp.Framework.Mvc;
using Domain;
using vm = ViewModel;

namespace Controllers
{
    [EscapeGCop("Auto generated code.")]
    public partial class IntelligentlyEnteredDataEnterController : BaseController
    {
        [NonAction, OnBound]
        public void OnBound(vm.IntelligentlyEnteredDataForm info)
        {
            // some on-bound code
            // ...

            info.WeekEndingDateFrom_Visible = info.Automated == false;
            info.WeekEndingDateTo_Visible = info.Automated == false;

            // some more on-bound code
            // ...
        }

        // some more controller methods
        // ...
    }
}

namespace ViewModel
{
    [EscapeGCop("Auto generated code.")]
    public partial class IntelligentlyEnteredDataForm : IViewModel
    {
        public IntelligentlyEnteredData Item { get; set; }
        
        [Required]
        public bool Automated { get; set; }
        
        [WeekEndingDate(ErrorMessage = "The week ending date is not valid.")]
        [Required(ErrorMessage="The Week ending date from field is required.")]
        public DateTime? WeekEndingDateFrom { get; set; }
        
        [WeekEndingDate(ErrorMessage = "The week ending date is not valid.")]
        [Required(ErrorMessage="The Week ending date to field is required.")]
        public DateTime? WeekEndingDateTo { get; set; }

        // some other view-model members
        // ...

        [ReadOnly(true)]
        public bool WeekEndingDateFrom_Visible { get; set; }
        
        [ReadOnly(true)]
        public bool WeekEndingDateTo_Visible { get; set; }
    }
}
```

The relevant snippet of Razor markup in our view file then will be:

```html
@if (Model.WeekEndingDateFrom_Visible)
{
<div class="form-group required-item">
    <div class="group-label">
        <label for="WeekEndingDateFrom" class="control-label">
            Week ending date from
        </label>
    </div>
    <div class="group-control">
        @Html.CustomDatePickerFor(x => x.WeekEndingDateFrom)
    </div>
</div>
}
@if (Model.WeekEndingDateTo_Visible)
{
<div class="form-group required-item">
    <div class="group-label">
        <label for="WeekEndingDateTo" class="control-label">
            Week ending date to
        </label>
    </div>
    <div class="group-control">
        @Html.CustomDatePickerFor(x => x.WeekEndingDateTo)
    </div>
</div>
}
```