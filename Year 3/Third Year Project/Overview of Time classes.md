# Time
## Static Members
- `static double offset = 0.0`: Is set when the first Time object is constructed in the runtime of the program. Stores a unix timestamp with second precision (presumably, is a double because other fields are doubles. The decimal places are never used.)
- `static int initialized = 0`: Boolean value; if a Time object hasn't been constructed yet then this will be 0, indicating that `offset` must be set. After this, initialized will stay as 1 for the runtime of the program.
- `static void Initialize()`: Is run when `initialized == 0`, a single time when the first Time object is constructed. Sets `offset` using the current time and sets `initialized` to 1.
- `static Time Now()`: Constructs and returns a new Time object with the current time.
- `static int junkInt`: Used as a default argument for some functions that take references to integers as arguments. Funny.
## Fields
- `double rep`: Stores seconds that have elapsed since `offset`. While offset is only accurate to a second, rep is accurate to a microsecond. The actual time since epoch is `(offset + rep)`.
## Methods
- `void Time::Convert(timeval&)`: Convert this Time object into a unix timeval struct, given as a reference.
- `void Time::BreakDown(lots of references, char *tz)`: Split this Time object into hour, min, second, weekday, etc. according to some timezone string given by `tz` or by the timezone defined in the env vars. Return the individual parts in the dozen references given as arguments.
- `void Time::BreakDownClock(lots of references)`: Split this Time object into clock time, i.e. hour, min, sec, millisecond. Oddly, doesn't let you provide a timezone.
- `void Time:BreakDownDate(lots of references)`: Same, but for calendar times, i.e. day of month, day of week, month of year, year. Also doesn't let you provide a timezone.

# Duration
Is an incredibly convoluted wrapper around literally a single double value. Just replace this with chrono::duration (or dont touch it at all who cares)

# WeekDay
Another class like Duration that's basically a wrapper around a single double value. Represents a day of the week (i.e. 1 = Sunday, 7 = Saturday). Offers conversion to string names, as well.

# Month
Similar to WeekDay, but it does actually contain two copies of certain bits of data in order to return correct values on leap years.

# Year
Includes some maths to deal with leap years (at least, i think thats what it is?). Like the rest of these, i imagine it can be replaced with a chrono class and it only exists cause of how old the program is.
Presents this interface:
- `static int IsLeap(int year)`: return true if year is leap. Can be replaced by `chrono::year.is_leap()`, which isn't a static method, so use time literals instead (e.g. `(2020y).is_leap()` vs `Year::IsLeap(2020)`) or construct a `year` object
- `static int Size(int year)`: returns the number of days in a year. Just check if the year is a leap year and use 366 instead of 365. Doesn't actually have a direct equivalent in chrono cause its so trivial.
- `static First()` and `static Last()` are just `chrono::year::min` and `chrono::year::max`.
- `static long Offset(int year)`: Gives the number of days between the first year and the current one. This is only used to construct the `Date` object, as far as I can tell.
- There's a bunch of stuff about centuries to handle century leap years.

Chrono example that gives the same results as `Offset`:
```cpp
int main() {
    const days d_dur = duration_cast<days>(year(2020) - 1900y);
    std::cout << d_dur.count() << "\n";
    std::cout << Offset(2020);
}
```
1900y is just an arbitrary year, to get the same number as Offset. The range could be much greater than the original program (you know, if the year 2400 wasn't enough future proofing for you.)

# Date
Real monster of a class. Hypothesis: 