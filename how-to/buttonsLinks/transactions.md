# Transactions

## Problem

Transaction management is an integral part of any business web application.
In the real world, a business transaction is a set of actions
that should only be considered as a single unit of business work,
usually involving some service actioned or the flow of some resource. For example, a
cash transaction involves a withdrawal from one account and a deposit into another.
If any action within a transaction fails 
the transaction is deemed to have failed even if multiple actions succeed,
and the business/application is rolled back to its previous state.
In software development, almost always by transaction we are referring to a database transaction.
To maintain database integrity, either all database
statements should successfully execute; or none of them should.

Not all series of actions involve database transaction, however.
Generally we want to exact control on which sets of actions we wrap up in a
transaction scope.

## Implementation

`Workflow.RunInTransaction()` takes a single Boolean parameter which has the default value `true`.
Therefore when more than one `WorkflowActivity` is added to the workflow,
actions are wrapped up as a transaction by default.

#### Example

Let's consider a customer form that is used to run tasks on some input data, yet the data is not persisted
within a database. We pass the parameter `false` to the `RunInTransaction()` method.

```csharp
using Domain;
using MSharp;

namespace Modules
{
    public class AgenciesEmailTemplateForm : FormModule<AgencyEmailTemplate>
    {
        public AgenciesEmailTemplateForm()
        {
            HeaderText("Customer Details");

            //================ Buttons: ================

            Button("Cancel")
                .OnClick(x => x.ReturnToPreviousPage());
            Button("Run task").IsDefault()
                .OnClick(x =>
                {
                    x.RunInTransaction(false);
                    x.CSharp("// some custom action");
                    x.CSharp("// some other custom action");
                });
        }
    }
}
```

Note that our two actions are not wrapped in a transaction scope: If an error occurs in one, changes that may occur
in the other will not be rolled back.

```csharp
using System;
using System.Web.Mvc;
using System.Web.Routing;
using MSharp.Framework.Mvc;
using Domain;
using vm = ViewModel;

namespace Controllers
{
    public partial class AgenciesSettingsEmailTemplatesEnterController : BaseController
    {
        [HttpPost, Route("AgenciesEmailTemplateForm/RunTask")]
        [ValidateAntiForgeryToken]
        public ActionResult RunTask(vm.AgenciesEmailTemplateForm info)
        {
            // some custom action
            
            // some other custom action
            
            return JsonActions(info);
        }
    }
}

namespace ViewModel
{
    [EscapeGCop("Auto generated code.")]
    public partial class AgenciesEmailTemplateForm : IViewModel
    {
        public AgencyEmailTemplate Item { get; set; }
    }
}
```