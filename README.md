# Glabol Himachal: landing page (draft)

The Himachal Backpacking Trip page, built from the Kashmir page and the "Glabol Trip Pages" design system. It has the same structure, form, Google Sheet, Glabol CRM and GTM tracking as Kashmir; only the content and colours change.

**Status: draft.** The structure is ready, but the trip facts wait for the Himachal brochure and photos. Every gap reads "TBC" on the page, and `python3 tools/build-zip.py` lists them all and refuses to build while any remain. The page also carries `noindex` until launch.

```
index.html       the landing page
thank-you.html   shown after an enquiry: 3 qualifying questions, then "your trip is on its way"
styles.css       all styling, shared by every destination; it holds no destination colours
palette.css      this destination's colours, generated from palettes/himachal-a.json (don't edit by hand)
site.js          settings (sheet URL, CRM, DESTINATION = "Himachal") and the events GTM listens for
main.js          landing page: form, ad attribution, keyword headlines, altitude chart, batches
thank-you.js     the qualifying questions and the Hot / Warm / Cold rule
images/          the photos (only the Glabol logo so far)
```

## 1. Before launch

From the **Himachal brochure**:
- the day-by-day itinerary: day cards (titles, text, altitude) and, if the trip climbs, the altitude chart with one stop per day (uncomment it in the route section);
- nights, highlights and the supporting line under the headline;
- prices per person, boarding and drop points, and the second option if there is one: title, meta tags, structured data, hero facts, price box, FAQ and the thank-you budget question;
- inclusions, exclusions, packing list, payment terms, terms and cancellation policy (the terms are Kashmir's for now; check they're the same);
- every batch date: one `<li data-start="YYYY-MM-DD">` row each in the batches section. Past batches hide themselves, the next six show and the hero's "next batch" fills in on its own;
- the four proof figures, the FAQ answers and the keyword lines in `matchHeadline()` (`main.js`).

From **you**:
- **Photos.** See section 2.
- **Rating.** Confirm that 4.5/5 from 1,685 reviews covers Himachal too.
- **Reviews.** Three real reviews from Himachal travellers (name, city, month).
- **Colours.** Pick one of the three palettes below.
- **Address.** The subdomain (himachal.glabol.com is assumed; the Glabol CRM already accepts it) and the GitHub repo to publish from.

Then:
- **Sheet email alerts:** the shared Apps Script's subject line says "New Kashmir lead" for every lead. Update it to name the destination (a one-line change, then Deploy → Manage deployments → New version);
- delete the `noindex` line and the draft note;
- run `python3 tools/build-zip.py` until it builds;
- take screenshots at 390, 820, 1280, 1440 and 1920px;
- send one test enquiry through a local copy, never the live page, so no real conversion fires.

## Colours (palettes)

Three Himachal candidates, all passing the contrast floors. **A is applied for now**; the pick is yours.

| Palette | Ground | Ink | Buttons | The idea |
| --- | --- | --- | --- | --- |
| `himachal-a.json` Snowline | #DCE6EB snow blue | #0F2933 deodar slate | #F5B53F marigold | snow peaks, deodar forests, temple marigolds |
| `himachal-b.json` Kullu shawl | #EEE4D3 undyed wool | #2C1323 cap-velvet plum | #C93468 Kullu pink, white label | the borders of a Kullu shawl and cap |
| `himachal-c.json` Deodar dusk | #E2E0EC twilight lavender | #191B3B night indigo | #FF9F45 apricot | Himalayan evenings and apricot orchards |

```bash
python3 tools/palette.py palettes/himachal-b.json --check   # contrast report only
python3 tools/palette.py palettes/himachal-b.json           # apply: writes palette.css and the browser theme colour
```

`styles.css` never changes per destination. The wordmark size is set on the word in `index.html` (`--wordmark-size: 20cqw` for HIMACHAL's 8 letters).

## 2. Photos

Drop them into `images/` under these names. Until a photo exists, its slot shows a block in the palette's colours, labelled with the file name.

| File | What | Size |
|---|---|---|
| `hero.jpg` | Himachal landscape, calm sky or snow on the left (the headline sits there) | landscape, about 2000px wide, under 350 KB |
| `hero-card.jpg` | a Glabol group photo for the top of the form | 800 × 360 |
| `avatar-1.jpg` … `avatar-3.jpg` | three traveller faces | 80 × 80 |
| `day-1.jpg` … `day-7.jpg` | one photo per day card (rename or add cards to match the itinerary) | 800 × 600 |
| `mood-1.jpg` … `mood-6.jpg` | six real group moments for the dark photo grid, each with a one-line caption | 1024 wide for mood-1, about 800 for the rest |
| `final.jpg` | a group photo for the final section and the thank-you page | 960 × 922 |

Resize and compress with `tools/img.py` (uses macOS `sips`):

```bash
python3 tools/img.py ~/Desktop/new.jpg images/day-3.jpg 800 58
```

Free-licensed photos (e.g. Wikimedia Commons) need a credit line in the footer, as on the Kashmir page.

## 3. Leads go to a Google Sheet

Himachal uses the same Google Sheet and Apps Script as Kashmir (the same `FORM_ENDPOINT`). The "Landing page" column shows which page a lead came from. Every enquiry becomes a row in the sheet straight away, and the sheet owner gets an email alert with a link to message the lead on WhatsApp. The thank-you page then asks 3 qualifying questions, and each answer is added to the same row. The sheet columns are:

| Timestamp | Name | Phone | Travel month | Travellers | Lead quality | Budget fit | Booking timeline | Best time to call | Source | Medium | Campaign ID | Adset ID | Ad ID | Keyword | GCLID | Landing page | Form | Lead ID |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|

- **Setup:** follow the 5 steps at the top of [`tools/google-sheet-leads.gs`](tools/google-sheet-leads.gs), then put the web app URL in `FORM_ENDPOINT` at the top of `site.js`.
- **Updating the script:** paste the new version and click Save. Then go to Deploy → Manage deployments → pencil → Version: "New version" → Deploy. "New deployment" would give it a new URL. Columns added in an update appear on the right of an existing sheet, and you can drag them anywhere.
- **Your own columns:** add "Status" or "Notes" columns, or reorder them, anywhere in the sheet. Leads are matched to columns by header name. Hide a column you don't need rather than deleting it, or it comes back.
- **Junk filter:**
  - The script only accepts a name plus a valid Indian mobile number.
  - Answers are only accepted with the exact button values, and they never change the name or phone.
- **Email alerts:** sent the moment the enquiry arrives, before the questions, so nobody waits for a call. A free Gmail account can send about 100 a day. Leads are still saved after that.
- **Glabol CRM:** the page posts every lead to the CRM itself, alongside the Sheet; see "Leads to the Glabol CRM" below. Keep the script's own `CRM_WEBHOOK_URL` empty, or each lead would arrive in the CRM twice.

While `FORM_ENDPOINT` is empty, submitting logs the lead to the browser console and still goes to the thank-you page, so you can test the flow. Other services also work in `FORM_ENDPOINT`, for example Formspree, or Web3Forms with `FORM_EXTRA_FIELDS = { access_key: "YOUR_KEY" }`. Those services save the first form only, not the answers.

**Leads to the Glabol CRM.** On submit, the page sends each lead to the Sheet and to `crm.glabol.com` at the same time. The CRM settings are in `site.js`: `CRM_WEBHOOK_URL`, `CRM_API_KEY` and `DESTINATION` (set `DESTINATION` per destination page). The page sends JSON with an `x-api-key` header:

| Field | Value |
|---|---|
| `name`, `phone` | from the form; phone as +91XXXXXXXXXX |
| `email` | empty (the form has no email field) |
| `destination` | `DESTINATION`: "Himachal" |
| `message` | "Interested in Himachal package. Travel month: Nov 2026. Travellers: 2." |
| `host` | the page's domain: himachal.glabol.com |
| `source` | "Google Ads" for Google ad clicks (gclid, or google / cpc), "Meta Ads" for Facebook or Instagram, "Website" for everything else |

Things to know:
- The CRM accepts requests only from `*.glabol.com` pages, so leads from a local test copy don't reach it.
- If the CRM is down or slow, the visitor still reaches the thank-you page (the page waits at most 5 seconds) and the lead is still in the Sheet. A failure is logged in the browser console only.
- The key is visible in the page source, like any browser-side webhook; the CRM should treat this endpoint as public.
- The thank-you answers go to the Sheet only, not to the CRM.

**Qualifying questions (thank-you page).** One question per screen, tap to answer, with Back and "Skip, just call me". Skipping loses nothing, because the lead is already saved.

1. **Budget fit:** "Yes, that fits" / "A bit high, but I'm open" / "I need something cheaper".
2. **Booking timeline:** "This week" / "In the next 2–4 weeks" / "Just exploring for now".
3. **Best time to call:** Morning / Afternoon / Evening / Anytime. The thank-you message then confirms the time they picked.

**Lead quality** (`leadQuality()` in `thank-you.js`):

| Lead quality | When |
|---|---|
| **Hot** | budget fits or "a bit high, but open", and booking this week or within 2–4 weeks. Call these first. |
| **Warm** | budget OK, but just exploring. |
| **Cold** | needs something cheaper. |
| **Partial** | answered the budget question only. |
| **Not answered** | skipped the questions or left the page. |

To change a question or an answer, edit its button in `thank-you.html`. Then add the same `value` to the `ANSWERS` list in the Apps Script, or the sheet ignores it, and update `leadQuality()` if the rule should change.

## 4. Ad tracking

**Google Ads final URL suffix.** Set it once for the whole account under Admin → Account settings → Tracking → Final URL suffix. You can also set it per campaign under Campaign settings → Additional settings → Campaign URL options.

```
utm_source=google&utm_medium=cpc&campaign_id={campaignid}&adset_id={adgroupid}&ad_id={creative}&kw={keyword}
```

Google fills in the `{…}` values on every click. "Adset ID" holds the Google Ads ad group ID.

**Meta (Facebook / Instagram) ads.** Put this in each ad's URL parameters:

```
utm_source=facebook&utm_medium=paid_social&campaign_id={{campaign.id}}&adset_id={{adset.id}}&ad_id={{ad.id}}
```

**How the columns are filled:**
- **Source and Medium** come from `utm_source` and `utm_medium`. Without them, the page works them out:
  - a Google Ads click is `google / cpc`;
  - organic search is, for example, `google / organic`;
  - another website is `site / referral`;
  - no referrer is `(direct) / (none)`.
- **Campaign ID** falls back to `gad_campaignid`, which Google's auto-tagging adds. Google Ads leads therefore get a campaign ID even if the suffix is missing.
- **Values Google can't fill** are left blank. For example, Performance Max has no ad group or ad ID, and Demand Gen has no keyword.
- **Credit for the ad click:** attribution is saved when the visitor lands, so the lead is credited to the ad click even if they browse around or reload first.

**Google Tag Manager (`GTM-TTXFHSF9`).** Every ad tag lives in GTM: Google Ads, GA4 and the Meta Pixel. The container snippet is at the top of `index.html` and `thank-you.html`, and the page itself loads no pixel. The page pushes these events to `dataLayer`, all from the thank-you page and only after a real enquiry. A reload or a direct visit sends nothing.

| Event | When | Data | GTM tags on it (container v17) |
|---|---|---|---|
| `lead_form_submit` | once per enquiry, as the thank-you page opens | `lead_id`; `user_data.phone_number` (+91…, read by the "User Provided Data" variable) | GAds user-provided data, GA4 `generate_lead`, Meta `Lead` |
| `thank_you_page_view` | straight after it | `lead_id`, `transaction_id` (= lead ID) | Google Ads conversion |
| `qualified_lead` | once, when the answers make the lead **Hot** (budget OK, booking within a month) | `lead_id`, `budget_fit`, `booking_timeline` | none yet |
| `lead_questions_complete` | once, when all three questions are answered | `lead_id`, `lead_quality` (Hot/Warm/Cold), `budget_fit`, `booking_timeline`, `call_time` | none yet |

GTM already loads the Meta Pixel and fires PageView on every page.

**Making Meta and Google optimise for quality:**
1. In GTM, add a Meta Pixel tag (custom event `QualifiedLead`) on a Custom Event trigger for `qualified_lead`.
2. If you like, add a Google Ads conversion tag ("Qualified lead") on the same trigger.
3. In Meta Events Manager, create a custom conversion from `QualifiedLead`.
4. Run ad sets on Lead until `QualifiedLead` reaches about 50 a week, then switch them to the custom conversion.

`lead_questions_complete` with `lead_quality` lets you build audiences or reports by lead quality.

**Every new destination page** keeps this exact setup: the same GTM container, the same event names and the same data. That way the existing GTM tags work on it without changes.

**Other tracking:**
- **Auto-tagging:** keep it on in Google Ads. The `GCLID` column lets you import offline conversions (leads that became bookings) later.
- **Keyword-matched line:** the `kw={keyword}` part of the suffix also picks the hero's supporting line. The list is empty until the brochure arrives; then add lines for solo, budget, group, the main places and each boarding city. Raw search text is never shown. Edit the list in `matchHeadline()` in `main.js`.
- **Privacy line:** the footer says that Meta and Google ad tools measure the ads and may receive the phone number in hashed form. Keep it while those tags run.

## 5. Test

```bash
cd ~/himachal-backpacking && python3 -m http.server 8080
# open http://localhost:8080/?utm_source=google&utm_medium=cpc&campaign_id=111&adset_id=222&ad_id=333&kw=himachal+solo+trip&gclid=test123
```

A local copy posts to the real Sheet and CRM only if you submit the form, so submit only with the endpoints in `site.js` pointed at a test copy.

## 6. Hosting: himachal.glabol.com

Same setup as Kashmir: GitHub Pages from the repo's `main` branch, behind Cloudflare, with a `CNAME` file holding `himachal.glabol.com`. Every push to `main` goes live in about a minute, so push only when the build script builds. GitHub Pages serves every file in the repo, so `tools/`, `palettes/` and this README are publicly reachable; nothing in them is secret. (`_headers`, `_redirects` and `.htaccess` only apply on Cloudflare Pages or Hostinger.)
