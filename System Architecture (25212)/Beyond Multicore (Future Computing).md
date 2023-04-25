## Reminders
- The difference between Snoopy and directory-based cache coherence protocols
	- **Global view** vs **local view** with a directory
	- **Minimal info** vs **extra info** for directory and remote shared lines
	- **Centralised communications** vs **parallel communications**
	- **Poor scalability** vs **better scalability**
- The concept of **false sharing**:
	- A behaviour that arises when two unrelated variables are stored in the same cache line
	- If both variables are written by two different cores often, they will generate a lot of invalidate/update traffic

