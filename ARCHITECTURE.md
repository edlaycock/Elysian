# Elysian Collection — Project Architecture

Last updated: 7 August 2026

## Where things live

| What | Where |
| --- | --- |
| Live site | https://elysiancollection.co.uk (hosted on Vercel) |
| Source repository | GitHub — `edlaycock/Elysian` |
| Deployment | Vercel, auto-deploys on commit to the default branch |
| Source assets (photos, logos, originals) | [Google Drive folder](https://drive.google.com/drive/folders/161Q-ofLhbafNyEz6XCXuKhhP7J_5MXUJ?usp=drive_link) |
| Design working file | `Elysian Collection.dc.html` (this project) |
| Deployable build | `deploy/` (this project) |

## Repository / deploy structure

```
index.html                     the entire site — markup, styles and logic in one file
support.js                     runtime required by index.html
image-slot.js                  drag-and-drop image placeholder component
assets/
  logo-stacked.png             full stacked logo (home hero)
  logo-white.png               light logo (footer)
  favicon.svg                  EC monogram favicon (primary)
  favicon-32.png               32px PNG fallback
  apple-touch-icon.png         180px rounded icon for iOS home screen
  gill-shannon.jpg             founder portrait
  manafaru-water-villas.jpg    JA Manafaru, Maldives
  palm-tree-court.jpg          JA Palm Tree Court, Dubai
  beach-hotel.jpg              JA Beach Hotel, Dubai
  ocean-view-hotel.jpg         JA Ocean View Hotel, Dubai (also contact hero)
  hatta-fort.jpeg              JA Hatta Fort Hotel, Dubai
  mar-hall.jpg                 JA Mar Hall, Scotland
  bruntsfield.jpg              JA The Bruntsfield, Scotland
```

Total image payload is roughly 5.3 MB; all photographs are resized to 2400px on the long edge.

All ten properties now have photographs, including `lake-view.jpg`. Note that the Lake View image is only 654x436 — a higher-resolution original from the Drive folder would improve it.

## Site structure

The site is a single HTML file using hash-based routing. Each route swaps the page content and updates the document title and meta description.

| Route | Page | Key sections |
| --- | --- | --- |
| `#home` | Home | Logo hero, three principles, founder profile (Gill Shannon), featured properties, services preview, closing call to action |
| `#services` | Services | Hero, six capabilities grid, four-step process, the collection (Luxury / Portfolio / UK), closing call to action |
| `#contact` | Contact | Hero, direct contact details (email, telephone, address, LinkedIn), enquiry form |
| `#privacy` | Privacy Policy | Eleven sections, UK GDPR and Data Protection Act 2018 |
| `#cookies` | Cookie Policy | Five sections, PECR |

Legal pages share one template driven by a data array, so editing policy copy means editing the `PRIVACY` or `COOKIES` arrays in the logic block — not the markup.

## Design system

- **Headings:** Cormorant Garamond, weight 300–400
- **Body:** Manrope, weight 300–600
- **Palette:** `#FBF9F5` / `#FFFDF9` warm off-white, `#201E1B` near-black, `#55514B` body text, `#8B857C` and `#A8A29B` muted labels, `#E4DED4` and `#EAE4DA` rules, `#C4BDB0` accents
- **Header:** centred logo with navigation underneath; transparent over image heroes, frosted off-white once scrolled
- **Heroes:** locked to `min-height: 880px` on desktop with identical padding so all pages align
- **Motion:** logo and portrait fade and rise on scroll; drifting background imagery on Services; slow silver wave lines in the footer. All motion is disabled under `prefers-reduced-motion`

## Contact details used across the site

- Gill Shannon — gill@elysiancollection.co.uk — 07920 017175
- LinkedIn: https://www.linkedin.com/in/gill-shannon-20638253/
- Enterprise House, The Courtyard, Old Court House Road, Bromborough, Wirral CH62 4AB

## Enquiry form

The contact form posts as JSON to **FormSubmit** (formsubmit.co) using its AJAX endpoint. No account, dashboard or API key is involved — everything is configured in the code.

The endpoint is the `FORM_ENDPOINT` constant at the top of the logic block in `index.html`:

```
https://formsubmit.co/ajax/gill@elysiancollection.co.uk
```

**Activation:** the very first submission after deployment triggers a one-off confirmation email to that address. The activation link in it must be clicked once; the form delivers normally from then on. Until it is activated, submissions return an error and the visitor sees the fallback message with the direct email address.

Optionally, once activated FormSubmit provides a random alias (`https://formsubmit.co/ajax/abc123…`) which can replace the email address in the endpoint so it is not published in the page source.

**Configuration, all in code:**

- Recipient is the address in the endpoint itself: gill@elysiancollection.co.uk
- `_autoresponse` — the confirmation email sent to the enquirer. The text lives in the `AUTORESPONSE` constant directly above `FORM_ENDPOINT`; edit it there.
- `_subject` — "Website enquiry — [company name]"
- `_replyto` — the enquirer's email, so replying goes straight back to them
- `_template: 'table'` — formats the notification email as a readable table
- `_captcha: 'false'` — no captcha, per the honeypot approach below
- `_honey` — hidden honeypot field. FormSubmit discards any submission where it is filled, catching most bots without troubling real visitors.

**Fields sent:** Name, Hotel or company, Email, Telephone, Hotel website, Area of interest, Message, Consent given, Submitted from (page URL). Field labels are sent as human-readable keys so the notification email reads cleanly.

Success keeps the in-page thank-you state. Failures show an error with the direct email address as a fallback, so an enquiry is never lost silently.

## Deploy process

1. Make changes in `Elysian Collection.dc.html`.
2. Copy to `deploy/index.html`.
3. Commit `index.html` (and any new files in `assets/`) to the GitHub repository.
4. Vercel redeploys automatically.

## Known open items

- If Elysian Collection is a registered company, the company number and registered office should be added to the footer and Privacy Policy.
