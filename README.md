# Kundly Bokning

A WordPress plugin that lets anyone add appointment booking to their site. Visitors book a time through a popup with a calendar view, bookings collect in WP-admin, and the whole booking calendar can be subscribed to in Google Calendar, Apple Calendar or any calendar app via an **iCal feed**.

**Version:** 2.6.1 · **Requires:** WordPress 5.5+ · **Tested up to:** 6.5

> The plugin's UI and emails are in Swedish ("Kundly Bokning" = "Kundly Booking").

---

## What it does

- Drops a booking button anywhere with the shortcode `[kundly_booking_button]`.
- The button opens a modal with a calendar view where the customer picks an open slot, enters name, email and an optional note, and confirms.
- Available slots are generated automatically from your weekly schedule, the meeting length and how far ahead booking is allowed — already-booked slots are removed.
- Every booking is stored in WP-admin under **Kundly Bokning → Bokningar**, with a WP dashboard widget for a quick overview.
- Customers can cancel themselves via a link in the confirmation (token-based, no login required).

## iCal subscription

The plugin exposes a private, token-protected iCal feed (`.ics`) of all bookings. Subscribe to it and the calendar updates automatically as bookings are added or cancelled.

You'll find the feed URL under **Settings → Calendar feed (iCal)**. From there you can copy the URL or generate a new token if it leaks (existing subscriptions then stop working and must be re-added).

How to subscribe:

- **Google Calendar:** Other calendars → From URL → paste the feed URL.
- **Apple Calendar (Mac):** File → "New Calendar Subscription" → paste.
- **iPhone:** Settings → Calendar → Accounts → Add Account → Other → Add Subscribed Calendar.

## Notifications

- **Email:** When a booking is confirmed, a confirmation email with a calendar attachment (`.ics`) is sent to the customer, and a notification to you. Email can be toggled on/off, and the sender name and notification address are configurable.
- **Webhooks:** An HTTP POST can be sent on confirmation and/or cancellation to a URL you set — handy for Make.com, Zapier or custom integrations. Cancellation can use its own URL; otherwise it falls back to the confirmation URL.

## Installation

1. Upload the `kundly-booking` folder to `/wp-content/plugins/`, or install the ZIP via **WP-admin → Plugins → Add New → Upload**.
2. Activate **Kundly Bokning** under **Plugins**.
3. Go to **Kundly Bokning → Inställningar** (Settings) and set the name, meeting length, weekly schedule, colors, email and any webhooks.
4. Add the shortcode `[kundly_booking_button]` to any page or post.

## Shortcode

```text
[kundly_booking_button]                  Uses the button text from settings
[kundly_booking_button text="Boka demo"] Custom button text
```

## Settings at a glance

| Setting | Description |
|---|---|
| Name, role, avatar, logo | Shown in the booking modal |
| Accent / hover color | Styles the button and modal |
| Meeting length | Length of each bookable slot (minutes) |
| Weekly schedule | Opening hours per weekday (supports multiple ranges) |
| Days ahead | How far into the future booking is allowed |
| Button text | Default label on the booking button |
| Meeting link | E.g. a video link included in the confirmation |
| Email | On/off, sender name and notification address |
| Webhook (confirmation / cancellation) | Target URLs for outgoing POSTs |
| Calendar feed (iCal) | Feed URL and token management |

## Webhook payloads

Sent as `application/json` with the header `User-Agent: Kundly-Booking/2.6.1`.

**Confirmation** — `event: "booking.confirmed"`:

```json
{
  "event": "booking.confirmed",
  "booking_id": 42,
  "staff_name": "Anna",
  "start": "2026-05-20T10:00:00+02:00",
  "end":   "2026-05-20T10:30:00+02:00",
  "duration": 30,
  "customer": { "name": "...", "email": "...", "notes": "..." },
  "site": { "name": "...", "url": "..." }
}
```

**Cancellation** — `event: "booking.cancelled"`: same structure as above, plus a `cancelled_at` field (ISO 8601).

## File structure

```text
kundly-booking/
├── kundly-booking.php     Main file: hooks, admin, booking logic, iCal, webhooks, email
├── assets/
│   ├── admin.css / admin.js       WP-admin (settings, copy/regenerate token)
│   └── frontend.css / frontend.js The booking modal
└── readme.txt             WordPress readme (plugin-directory format)
```

## Changelog

**2.6.1**
- Subscribable iCal feed (Google/Apple/iPhone), with a regeneratable token
- Confirmation and cancellation emails with an `.ics` attachment
- Separate webhooks for confirmation and cancellation
- Token-based self-cancellation for customers
