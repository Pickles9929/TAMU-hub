# Aggie Hub

A one-page daily dashboard for a Texas A&M student: College Station weather, today's classes, what's due, weekly dorm errands, course cards with a practice-test launcher, and quick links to campus sites.

## Using it

Open `index.html` in a browser, or host it anywhere that serves static files (GitHub Pages works). Click **Edit courses & class times** to set up your courses, Claude Project links, class schedule and name.

## To-dos

Add a to-do with an optional due date, and choose whether it repeats (every day, week, 2 weeks or month). When you check off a repeating to-do, it moves to **Coming up** and comes back on its next date.

### Email to-dos

When Aggie Hub is opened from its Claude link, it can turn emails into to-dos by reading your Gmail through Claude's Gmail connector. Turn it on in **Edit courses & class times**, then **Email to-dos**.

- **Any email:** forward it to your own TAMU address and type `todo` in the message.
- **Emails that come often**, like package notices: add a rule. A rule is a Gmail search, such as `from:mailroom@tamu.edu`, and every new email that matches becomes a to-do.

Aggie Hub only searches with your rules. It never changes or sends email. This feature isn't available when the page is opened as a plain file or from GitHub Pages.

## Your data

Everything is saved in your browser's local storage. Nothing is sent anywhere. The only network request is the weather forecast from Open-Meteo.

To move your hub to another device or browser, open **Edit courses & class times**, then **Download backup**. On the other device, use **Load backup** and choose that `.json` file.

The page follows your system's light or dark mode.
