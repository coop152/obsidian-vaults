# Static Members
- `static double offset = 0.0`: Is set when the first Time object is constructed in the runtime of the program. Stores a unix timestamp with second precision (presumably, is a double because other fields are doubles. The decimal places are never used.)
- `static int initialized = 0`: Boolean value; if a Time object hasn't been constructed yet then this will be 0, indicating that `offset` must be set. After this, initialized will stay as 1 for the runtime of the program.
- `static void Initialize()`: Is run when `initialized == 0`, a single time when the first Time object is constructed. Sets `offset` using the current time and sets `initialized` to 1.
- `static Time Now()`: Constructs and returns a new Time object with the current time.
- `static int junkInt`: Used as a default argument for some functions that take references to integers as arguments. Funny.
# Fields
- `double rep`: Stores seconds that have elapsed since `offset`. While offset is only accurate to a second, rep is accurate to a microsecond. The actual time since epoch is `(offset + rep)`.
# Public