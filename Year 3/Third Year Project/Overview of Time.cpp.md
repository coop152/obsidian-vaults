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
Is an incred