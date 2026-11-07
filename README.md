# Aggie Hub

A one-page daily dashboard for a Texas A&M student: College Station weather, today's classes, what's due, weekly dorm errands, course cards with a practice-test launcher, and quick links to campus sites.

## Using it

Open `index.html` in a browser, or host it anywhere that serves static files (GitHub Pages works). Click **Edit courses & class times** to set up your courses, Claude Project links, class schedule and name.

## Your data

Everything is saved in your browser's local storage. Nothing is sent anywhere. The only network requests are for the College Station weather: the National Weather Service forecast (with current conditions from Easterwood Airport), with Open-Meteo as a backup. The last good forecast is saved so the card still shows something if both are down.

To move your hub to another device or browser, open **Edit courses & class times**, then **Download backup**. On the other device, use **Load backup** and choose that `.json` file.

The page follows your system's light or dark mode.
