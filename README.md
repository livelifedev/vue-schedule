# vue-schedule

Deployed:
https://vue-coach-scheduler.vercel.app/

## Project setup

```
yarn install
```

### Compiles and hot-reloads for development

```
yarn serve
```

### Compiles and minifies for production

```
yarn build
```

## Project notes

With limited time to work on this, from the beginning I made decisions on the scope and functionality that I could implement. One big decision to make was whether or not to use a time/date library, as I had limited experience with these types of libraries and learning one could possibly be a big time investment, I decided to write my own logic instead to handle the times - though consequently limit what I can do, such as handling time zones.

In a real project, I would invest more time in researching appropriate time/date libraries that can handle more complex functionality.

Other things to note are, only one booking at a time can be made, the previous is overwritten in localstorage when a new timeslot is booked. And gaps in time slots are not accounted for in the logic.
