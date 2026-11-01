# Wedding invitation

A mobile-first interactive wedding invitation with:

- A phone-width layout on desktop that fills the screen on mobile
- Animated envelope opening
- Invitation appears after the envelope opens
- Configurable invitation details (names, date, venue, schedule, dress code)
- Guest RSVP form with up to four named additional guests (five people total)
- Host dashboard to selectively approve or decline each requested guest
- Final itemized RSVP notifications via Resend

## Run locally

Requires Python 3.9+. No packages to install.

```powershell
$env:ADMIN_PASSWORD = "choose-a-private-host-password"
.\start.ps1
# or: python server.py
```

Open [http://localhost:8080](http://localhost:8080). The server is already designed for mobile touch (envelope tap + fog wipe).

Guests use the invitation link. The host dashboard is separate at [http://localhost:8080/host](http://localhost:8080/host) when running locally. The dashboard password comes from the server environment and is never embedded in the client.

## Customize for another wedding

Edit [`data/wedding.json`](./data/wedding.json):

| Field | Purpose |
| --- | --- |
| `couple` | First / second / display names |
| `date`, `time` | Celebration timing |
| `venue` | Name, address, map link |
| `photo`, `photoCaption` | Hero image + caption |
| `welcome`, `message` | Opening copy |
| `schedule` | Itinerary rows |
| `dressCode` | Attire guidance |
| `rsvpBy` | RSVP deadline |

Guest replies are stored in [`data/rsvps.json`](./data/rsvps.json). Keep that file private and back it up.

If you host the couple photo somewhere other than `images.unsplash.com`, update the `img-src` directive in `server.py`.

## Final RSVP notifications

When a host finalizes all requested guest decisions, the final RSVP and itemized approved/not-approved guest list are saved first. If Resend is configured, the primary guest receives an email with a link that opens the invitation directly. Set `INVITATION_URL` to the public base URL of the deployed app; do not use localhost for recipients outside your computer. Guests requesting additional attendees should provide an email address:

```powershell
$env:RESEND_API_KEY = "your-resend-api-key"
$env:RESEND_FROM = "Sofia & Oliver <rsvp@your-verified-domain.example>"
$env:INVITATION_URL = "https://your-public-domain.example"
```

Without those variables, decisions are still saved and the dashboard reports that no email was sent.

## Production notes

- Serve over HTTPS
- Use a strong unique `ADMIN_PASSWORD`
- Keep `data/rsvps.json` on private persistent storage
- Behind a TLS-terminating proxy, set `HOST=0.0.0.0` and `HTTPS=1` so the host session cookie is marked Secure

This file-backed server suits a single wedding invitation. For multi-instance hosting, move RSVPs and sessions to a shared database.
