# Limiting time picker hours/minutes

## Problem

Sometimes the time picker element selection choices should be limited as the business logic ditates this. 
In these cases the picker will need modifying in your M# code.

## Implementation

To customise maximum/minimum hours/minutes and minute intervals, chain the desired methods to your
`DateTimeFormElement` object.

#### Example

The client is a charity and requires inputting of shifts for their volunteers.
For administrative purposes the smallest time interval is required to be 10 minutes and
shifts should not start nor end on the hour. Furthermore, the organisation is only open
from 9am until 5pm and so all shifts should start and end between these two times.
We can implement this as follows:

```csharp
using MSharp;
using Domain;

namespace Modules
{
    public class VolunteerShiftForm : FormModule<VolunteerShift>
    {
        public ShiftForm()
        {
            Field(x => x.Volunteer).Control(ControlType.AutoComplete);
            Field(x => x.ShiftStart).Control(ControlType.DateTimePicker);
                .MinimumMinute("10")
                .MaximumMinute("50")
                .MinimumHour("9")
                .MaximumHour("16")
                .MinuteIntervals("10");
                
            Field(x => x.ShiftEnd).Control(ControlType.DateTimePicker);
                .MinimumMinute("10")
                .MaximumMinute("50")
                .MinimumHour("9")
                .MaximumHour("16")
                .MinuteIntervals("10");

            Button("Save").IsDefault().Icon(FA.Check).OnClick(x =>
            {
                x.SaveInDatabase();
                x.GentleMessage("Saved successfully");
                x.ReturnToPreviousPage();
            });
        }
    }
}
```