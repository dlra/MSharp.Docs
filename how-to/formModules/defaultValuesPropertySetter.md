# Default Values (Property Setter)

## Problem

Sometimes a form field has a natural default value which you would want to set
before the form page has been rendered. Then, the user may be able to edit this
value once the page is loaded--or not, if the field is set to read-only or even
hidden from the UI.

## Implementation
M# offers some property setting methods in its `AutomaticPropertySetter` class. These
can be used to set values for entity properties and are usually accessed by pointing to given
entity proprerties via the `Autoset()` method. Often you will want to set the property value
from a query string (QS) key-value pair. This is done in the background by default 
if the QS key is the same as the property name--to bind to different QS key-values, use the 
`FromRequestParam()` method. Otherwise you could use the `Value()` method to set the property 
value directly.

[//]: # (AutomaticPropertySetter.Value &AutomaticPropertySetter.FromRequestParam--->CustomBound attribute in the view-model definition)
[//]: # (ModuleProperty.FromRequestParam--->FromQuery attribute in the view-model definition)

When defining your own properties on the view-model using the `ViewModelProperty<TProperty>()` method,
things work slightly differently although not terribly so. `ViewModelProperty<TProperty>()` 
returns a `ModuleProperty` object, which also has  `FromRequestParam()` 
method which you may use.


#### Example
In this example, the Customer property is has a default setting and 
is not modifiable or visible on the UI.

We set in the module:

```csharp
using Domain;
using MSharp;

public class CategoryForm : FormModule<CustomerCategory>
    {
        public CategoryForm()
        {
            HeaderText("Category Details")
            
            Field(x => x.Name);
            AutoSet(x => x.Customer).Value("CurrentCustomer");
            
            // some other UI settings and components
        }
    }
```

This generates code so that the view-model property `Customer` is set in the
`OnBound` method (`info.Customer = CurrentCustomer`) on the controller and 
given the attribute `CustomBound` in its definition.

```csharp
namespace ViewModel
{
namespace Controllers
{
    [ViewData("LeftMenu", "CustomersSettingsMenu")]
    [ViewData("Title", "Customers > Settings > Categories > Enter")]
    [EscapeGCop("Auto generated code.")]
    public partial class CustomersSettingsCategoriesEnterController : BaseController
    {
        [NonAction, OnBound]
        public void OnBound(vm.CategoryForm info)
        {
            info.Item = info.Item ?? new CustomerCategory();
            
            if (Request.IsGet()) info.Item.CopyDataTo(info);
            
            info.Customer = CurrentCustomer;

            // some other OnBound code
            // ...
        }

        // some other controller code
        // ...
    }
}

    [EscapeGCop("Auto generated code.")]
    public partial class CategoryForm : IViewModel
    {
        public CustomerCategory Item { get; set; }
        
        [Required]
        [StringLength(200, ErrorMessage = "Name should not exceed 200 characters.")]
        public string Name { get; set; }
        
        [ReadOnly(true), CustomBound]
        public Domain.Customer Customer { get; set; }

        // some other view-model class members
        // ...
    }
}
```

We have