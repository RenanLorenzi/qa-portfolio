# QA Portfolio – Renan Lorenzi

QA Tester based in Zurich | ISTQB CTFL | PSM I

Manual, exploratory and test-case-based testing of web and mobile applications through crowdtesting platforms (Test IO, uTest).

> **About confidentiality:** all projects below were done under NDA. Client names, URLs, test data, bug IDs and screenshots are intentionally left out. Each case describes the type of product, what I tested, how I worked and what I learned.

---

## Case studies

| # | Product type | Testing type | Platform | Highlights |
|---|---|---|---|---|
| 1 | Swiss private bank – e-banking web app + mobile signing app | Test cases + exploratory | Test IO | 6 test cases, 1 approved bug, 5 user stories, reproductions |
| 2 | Hotel booking website | Exploratory | uTest | **High-severity** bug approved (currency edge case blocks booking) |
| 3 | E-commerce shop (staging) – concurrent-usage stress test | Exploratory under load | Test IO | Checkout failure analysed with DevTools (HTTP 400) |
| 4 | Cinema chain – Android app | Bug fix verification | uTest | Fix of a Critical bug verified and approved |
| 5 | News website – games section (mobile browser) | Test cases | uTest | Failed step linked to an existing bug instead of a duplicate |
| 6 | Swiss consumer brand – international website | Geo-IP & localization | Test IO | 4 locales verified with a controlled test environment |

---

### 1. E-banking: structured beneficiary addresses (Swiss private bank)

**Context:** Two test cycles on the staging environment of a private bank's e-banking web app (Oct 2026). The new feature split beneficiary addresses into separate fields (street, house number, ZIP, city, country) instead of free text, affecting domestic, international and QR-bill payments.

**Environment**
- macOS + Chrome (guest window); iPhone with the bank's signing app, installed via TestFlight
- Staging reachable only through the platform's proxy/VPN, configured on both devices
- Test users with 2FA (QR login and payment confirmation on the mobile app)

**What I did**
- **Scripted test cases:** executed 6 test cases covering new and known beneficiaries, partial/complete/no address, and QR-bill payments. Each step marked Passed / Failed, with evidence.
- **Bug found:** a payment option expected in the execution step was not displayed. Verified with two different IBAN countries before reporting. **Approved by the Test Lead.**
- **Reproductions:** confirmed existing bugs (e.g. incorrect payment status after validation) and reported results on a different OS / IBAN country than the original report.
- **Exploratory user stories:** 5 stories on login and payment flows (security banner, loss of connectivity, non-blocking payment screen, validation rules for new vs. known beneficiaries, manual rejection).
- Simulated loss of connectivity with **DevTools → Network → Offline**, so no personal network data appeared in recordings.

**What I learned**
- Read the exact precondition and step text before concluding something is a bug. One "bug" was the expected result of the step itself.
- Business rules explain many "bugs" (default execution date on next business day, *Cancelled* vs. *Rejected* status, known beneficiaries skip validation).
- Test data matters: I caught an invalid checksum in a sample IBAN provided in the test material.
- Plan the order of test cases: some cases create the data (beneficiaries) that others need.

---

### 2. Hotel booking: currency edge case blocks the booking flow

**Context:** uTest Academy qualification cycle (Sep 2026): exploratory testing on a live hotel booking website. To pass, a real bug report had to be approved.

**Environment:** macOS 27 + Chrome. Precondition: user not logged in, cache and cookies cleared.

**Finding:** After switching the display currency to **Bitcoin (XBT)**, every price on the search results (hotel cards and map pins) was shown as **"XBT 0"**. Clicking *Book now* led to *"There was an unknown error"*, and *Continue with booking* only reloaded the same error. The user could not continue the booking.

**The report**
- 12 steps starting from the homepage URL, one action per step
- Expected vs. actual result, plus the exact error message
- Highlighted screenshot + video

**Result:** **Approved – High severity**, functional, reproducible every time.

**What I learned**
- Settings that real users can choose but developers rarely combine (an unusual currency + the booking flow) are a good place to look for bugs.
- Severity follows impact: a bug that blocks the core flow (booking) is High, even if the trigger is uncommon.

---

### 3. Stress test: checkout failure analysed in DevTools (e-commerce staging)

**Context:** Concurrent-usage test (Oct 2026): many testers buying at the same time on a staging shop to see how the system behaves under load. In scope: slow pages, timeouts, 5xx errors, lost carts, failed or duplicated orders, payment errors.

**Setup:** Chrome guest window, DevTools Network tab docked with *Preserve log*, system clock with seconds visible in recordings, test payment cards and sandbox accounts from the instructions.

**What I did**
- Ran 7 complete end-to-end purchase flows (guest, new account, existing account; several payment methods).
- **Finding:** checkout via an external payment method failed with a generic error message. In the Network tab, the `order` request returned **HTTP 400** on every retry; the **Response** body said the cart was empty, although the UI still showed 3 items. After reloading, the cart was gone.
- My first hypothesis was a rejected address; the response body showed the real cause.
- **Duplicate check:** the same symptom and cause were already reported, so I submitted a **reproduction** of the existing bug instead of a new report, with video and screenshots of the Response body and the empty cart.

**What I learned**
- Read the **Response** tab, not just the status code: it changed the diagnosis.
- Check the existing bug list before reporting; reproduce instead of duplicating.
- Confirm the cycle's end time at the start and submit session reports with a safety margin.

---

### 4. Android app: verifying the fix of a Critical bug (cinema chain)

**Context:** Exploratory cycle with bug fix verification (BFV) test cases on a cinema chain's Android app, production build, covering several countries (Sep 2026).

**Task:** Verify the fix of a previously reported **Critical** bug: on the movie page, the *Rate* and *My list* buttons stopped responding after the app had been left unused for some hours.

**Environment:** Samsung Galaxy A57, Android 16, Swiss mobile network.

**How**
- Confirmed the installed app version matched the version required by the cycle before testing
- Respected the original bug's time-based precondition: left the app unused for 3 hours, then followed the original steps
- Evidence as required by the cycle: video starting from opening the app (showing the app country) and a screenshot of the app version from Settings

**Result:** **Passed – the fix was confirmed, test case approved.** Evidence: 3 screenshots + video.

**What I learned**
- Bug fix verification (confirmation testing) checks one specific fix; it is not the same as regression testing.
- Version evidence comes first: a verification on the wrong build is worthless.

---

### 5. News website: games section, test cases on a mobile browser

**Context:** Two test-case cycles on a news website's puzzle/games section (Sep and Oct 2026). Each slot defined an exact configuration: screen size range, browser, orientation and theme.

**Environment:** iPhone 15 Pro Max, iOS 27, Chrome, portrait (dark mode in cycle 1, light mode in cycle 2).

**Cycle 1 – page exploration (dark mode):** Device/OS evidence, setup screenshot, then exploration of styling and functionality in the assigned theme and orientation. **Passed, approved.**

**Cycle 2 – calendar and games check (light mode)**
- Played two puzzles of the day and checked whether the calendar changed their state to *Played*.
- **Actual:** the calendar still showed *Available* instead of the green *Played* tick, even after waiting several minutes, reloading and opening the archive in a new tab.
- The issue was already reported, so I marked the step **Failed**, linked it to the existing bug ID and added a **+1** (reproduction) instead of creating a duplicate.
- **Test case approved.**

**What I learned**
- In test-case cycles, a failed step must reference an issue ID: link to an existing bug when it exists.
- Rule out timing and caching (wait, reload, new tab) before concluding that a state does not update.
- Respect the slot configuration exactly (device size, orientation, theme): it is part of the test.

---

### 6. Geo-IP & localization: country and language redirection (Swiss consumer brand)

**Context:** Focused cycle (Oct 2026) verifying that the brand's website sends visitors to the right country (by IP) and the right language (by browser settings). Layout and purchases were out of scope.

**Approach: the environment *is* the test**
- Proxy and VPN switched **off** (both would route traffic through another country); IP location confirmed before testing
- Chrome language list set per test: only the top language counts
- New incognito window for each language (incognito windows share cookies while any is open)
- DevTools open + **Empty cache and hard reload** before reading each result

**Result:** All 4 Swiss locales (de-CH, fr-CH, it-CH, en-CH) redirected correctly. User story submitted as passed with 4 short videos showing the language setting and the final URL. No bugs found.

**What I learned**
- In geo/locale testing, network and browser state decide the result. Check them before every test.
- The access instructions of a cycle override the feature description when they differ (e.g. which URL to test).
- Prepare the recording setup (clean browser profile, no full-screen windows, Do Not Disturb) to avoid editing out personal data afterwards.

---

## How I write a bug report

- **Title:** what is wrong, where, and when (one line)
- **Steps:** one action per step, starting from the homepage URL and a clear precondition
- **Actual vs. Expected:** two different, specific statements
- **Environment:** device, OS, browser + version
- **Evidence:** short video with the URL visible and no personal data; highlighted screenshot; network evidence when relevant
- **Severity:** chosen against the platform's assessment criteria, based on user impact
- **Before submitting:** search existing bugs; if it already exists, reproduce it instead

---

## Tools & skills

- **Testing:** test case execution, exploratory testing, user stories, reproductions, bug fix verification
- **Browser:** Chrome DevTools (Network, Response, cache control, offline simulation)
- **Mobile:** iOS (TestFlight builds, mobile browsers), Android (native apps)
- **Environments:** proxy / VPN setup for staging access, 2FA test users
- **Evidence:** screen recording and video conversion for bug reports
- **Studying:** API testing with Postman, SQL basics

## Certifications

- ISTQB Certified Tester Foundation Level (CTFL)
- Professional Scrum Master I (PSM I)

## Next

- 🛠️ **In progress:** own testing project on a public demo application (test plan, test cases, bug reports, API tests with Postman)

---

📍 Zurich, Switzerland · 🔗 [LinkedIn](https://www.linkedin.com/in/renanlorenzi/)
