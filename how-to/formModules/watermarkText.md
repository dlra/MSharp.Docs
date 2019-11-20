# Watermark Text

## Problem

Sometimes it is unclear to the user exactly what content is required as input for a form field, and how this input should be formatted. To guide the user in their filling out of a form in your application, it can be a good idea to add watermark text.

## Implementation

The `WatermarkText()` method is a member of and has a return type of `FormElement` (and its derived classes).

#### Example

Suppose we want to save a message which will be sent out at a pre-determined date/time. We can design a message form using a `Message` entity defined in your `Model` (not shown):

```csharp
using Domain;
using MSharp;

namespace Modules
{
    class MessageForm : FormModule<Message>
    {
        public MessageForm()
        {
            HeaderText("Message Details");

            Field(x => x.SendDate_time)
                .WatermarkText("dd/MM/yyyy hh:mm")
                .Control(ControlType.DateAndTimePicker)

            // additional UI features
            // ...
        }
    }
}
```

This will generate a view file in your Website in Razor markup. Note the `placeholder` attribute in the `input` element for this form field:

```html
<div class="form-group row required-item">
    <label for="SendDate_time" class="control-label">
            Send date/time
    </label>
    <div class="group-control">
        <div class="input-group">
            <input type="text"asp-for="SendDate_time" class="form-control" placeholder="dd/MM/yyyy hh:mm" data-control="time-picker" />
            <span class="input-group-addon">
                <i title="pick a time" class="fa-clock-o fa"></i>
            </span>
        </div>
    </div>
</div>
```

This is rendered thus in your web browser:

![Watermark text](images/watermark-text.PNG)