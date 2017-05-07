# Problem of Spring Framework

## Configuration in XML
- Move beyond XML
- Do not program in XML
- Springless app, things are built, plugged in and ready to work
- With Spring, app needs 30-60 seconds to work. It initializes beans before failing over.

## Magic
- For XML, "you can do it all via annotation now! No more XML!"
- It is magic when it works, but you have no idea when it does not work.
- I do not like magic :(

## Importing other Spring files
- Break Spring files into smaller ones, and put them into different modules. :( How Tellriable it is!!!
- Spring fiels in jars is a bad idea. Every time you spread dependent spring files across jars, a child dies.