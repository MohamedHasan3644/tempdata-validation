# Manual Test Execution Guide

This guide gives literal click-by-click instructions for the 21 behavior testcases in the Static SSR sample. The numbering follows the supplied list (`TC-01` through `TC-12`).

## TC-01 - Direct reader with no prior submit

**Purpose:** Prove that the ordinary reader is empty when no message was submitted.

### Prepare

1. Follow **Open a clean Chrome session** above.
2. In the new Incognito window, enter `https://localhost:7171/read` directly. Do not visit `/` first.
3. Press **F12** and follow **Prepare Chrome DevTools**.
4. Because DevTools was opened after navigation, press **Ctrl+R** once to capture a request in Network. Treat this as the initial captured request.

### Verify the initial request

1. Confirm the address bar ends with `/read`.
2. Confirm the heading says **Ordinary consuming reader**.
3. Confirm **First read** displays `empty`.
4. Confirm **Second read** displays `empty`.
5. Write down the displayed **Request UTC** value.
6. Follow **Inspect an output element** for `#first-read`.
7. Confirm its markup is `<output id="first-read" data-has-value="false"></output>`.
8. Paste its outer HTML into `evidence\TC-01-markup.txt` under `Initial GET /read`.
9. Repeat the inspection for `#second-read`.
10. Confirm its `data-has-value` is also `false` and paste its outer HTML into the same file.

**Evidence checkpoint - capture now:** Before leaving **Elements**, keep the `/read` page visible beside DevTools and position the DOM so the complete `#first-read` and `#second-read` output lines are visible together. Include the address bar, **Ordinary consuming reader** heading, both visible `empty` results, both element IDs, and both `data-has-value="false"` attributes. Save as `TC-01-01-initial-markup.png`.

11. Follow **Inspect a Network request** for the row named `read`.
12. Confirm **Request Method** is `GET` and **Status Code** is `200 OK`.

**Evidence checkpoint - capture now:** Keep the newest `read` row selected on **Network > Headers**. Include the Network row and **General** section showing `https://localhost:7171/read`, `GET`, and `200 OK`. Save as `TC-01-02-initial-network.png`.

### Reload and verify again

1. Click the page area above DevTools.
2. Press **Ctrl+R** once.
3. Confirm both reads still display `empty`.
4. Confirm the new **Request UTC** differs from the value recorded earlier.
5. Inspect `#first-read` and `#second-read` again and confirm both attributes remain `false`.
6. Paste both reloaded elements into `evidence\TC-01-markup.txt` under `Reload GET /read`.

**Evidence checkpoint - capture now:** Before switching away from **Elements**, include the reloaded `/read` page, its new **Request UTC**, both visible `empty` results, and both complete output lines showing `data-has-value="false"`. Save as `TC-01-03-reload-markup.png`.

7. In Network, click the newest `read` row.
8. Confirm it is another `GET` with `200 OK`.

**Evidence checkpoint - capture now:** On **Network > Headers**, include the newest reloaded `read` row and **General** showing `/read`, `GET`, and `200 OK`. Keep the earlier `read` row visible in the request list so the two requests can be distinguished. Save as `TC-01-04-reload-network.png`.

9. Follow **Check for browser errors**.

**Evidence checkpoint - capture now:** Keep **Console** open with its complete visible message area and error counter in view. Include the `/read` heading above DevTools if space allows. Save as `TC-01-05-console.png`.

10. Look at the server PowerShell window and confirm no exception or `fail:` entry appeared.

### Pass checklist

- Initial and reload requests are both `GET /read` with `200 OK`.
- Both reads are empty on both requests.
- Both output markers are `false` on both requests.
- Request UTC changes after reload.
- No browser or server error occurs.

## TC-02 - One-time message after redirect

**Purpose:** Prove the POST-Redirect-GET flow and Must Hold 1.

**Test value:** `TC02-20260909-001` or a fresh equivalent.

### Prepare and submit

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Confirm the heading says **Seed a TempData message**.
4. Press **F12** and follow **Prepare Chrome DevTools**.
5. Click the **Message** textbox.
6. Press **Ctrl+A**.
7. Type the fresh test value.
8. Click the **Redirect target** list.
9. Click **Consume with ITempData**.
10. Confirm the textbox and target show the intended values.

**Evidence checkpoint - capture now:** Before submitting, capture the page only. Include the address bar, **Seed a TempData message** heading, complete **Message** value, selected **Consume with ITempData** target, and **Store and redirect** button. Save as `TC-02-00-seed.png`.

11. Confirm Network is open and **Preserve log** is checked.
12. Click **Store and redirect**.
13. Wait until the address bar ends with `/read`.
14. Do not reload yet.

### Verify POST and redirect

1. Click the **Network** tab.
2. Locate the request whose **Method** is `POST` and URL/name represents the root `/`.
3. Click that POST row.
4. Click **Headers**.
5. Under **General**, confirm **Request URL** is `https://localhost:7171/`.
6. Confirm **Request Method** is `POST`.
7. Record the actual **Status Code**. The expected result in this sample is `302 Found`.
8. Scroll to **Response Headers**.
9. Confirm **Location** is `https://localhost:7171/read` or `/read`.
10. Confirm a `Set-Cookie` header names `.AspNetCore.Components.TempData`.
11. Do not save the unredacted cookie value. Redact the value before keeping the image.

**Evidence checkpoint - capture now:** Keep the root `POST` row selected on **Network > Headers**. Include the selected row, **General** showing root URL, `POST`, and `302 Found`, plus the **Location** header and `.AspNetCore.Components.TempData` header name. Crop out the cookie value or cover the entire value with an opaque block before saving. Save as `TC-02-01-post-redirect-redacted.png`.

12. In the Network list, click the later row named `read`.
13. Under **Headers > General**, confirm its method is `GET` and status is `200 OK`.
14. Confirm this GET appears after the POST in the Network list.

**Evidence checkpoint - capture now:** Keep the later `read` row selected. Include both the earlier POST and later `read` rows in the Network list, and **General** showing `/read`, `GET`, and `200 OK`. Save as `TC-02-02-read-get.png`.

### Verify the first reader response

1. Look at the page above DevTools.
2. Confirm **First read** exactly equals the submitted value.
3. Confirm **Second read** exactly equals the submitted value.
4. Record **Request UTC**.

**Evidence checkpoint - capture now:** Before opening **Elements** or reloading, capture the rendered page. Include the address bar ending in `/read`, **Ordinary consuming reader** heading, both complete result values, and **Request UTC**. Save as `TC-02-03-first-read.png`.

5. Inspect `#first-read` and confirm `data-has-value="true"`.
6. Copy its outer HTML into `evidence\TC-02-markup.txt` under `Initial GET /read`.
7. Inspect `#second-read`, confirm `true`, and copy it into the same file.

**Evidence checkpoint - capture now:** On **Elements**, position the DOM so both complete output lines are visible. Include the rendered results and both IDs with `data-has-value="true"`. Save as `TC-02-04-initial-markup.png`.

### Reload and verify removal

1. Click the page area.
2. Press **Ctrl+R** once.
3. Confirm the URL remains `/read`.
4. Confirm **First read** now displays `empty`.
5. Confirm **Second read** now displays `empty`.
6. Confirm the new **Request UTC** differs from the first value.
7. Inspect `#first-read` and `#second-read`.
8. Confirm both now have `data-has-value="false"`.
9. Copy both reloaded elements into `evidence\TC-02-markup.txt` under `Reload GET /read`.

**Evidence checkpoint - capture now:** Before leaving **Elements**, include the reloaded page with both `empty` results and the new **Request UTC**, plus both output lines showing `data-has-value="false"`. Save as `TC-02-05-after-refresh.png`.

10. Click the newest `read` request in Network.
11. Confirm it is `GET /read` with `200 OK`.
12. If its response clears the TempData cookie, record only the empty cookie name and expiry attributes.

**Evidence checkpoint - capture now:** On **Network > Headers**, include the newest `read` row and **General** showing `/read`, `GET`, and `200 OK`. If cookie-clearing metadata is included, show only the cookie name and expiry attributes; redact any value. Save as `TC-02-06-refresh-get.png`.

13. Follow **Check for browser errors**.

**Evidence checkpoint - capture now:** Keep **Console** open with the complete visible message area and error counter. Include the `/read` heading if space allows. Save as `TC-02-07-console.png`.

14. Confirm the server terminal contains no exception or `fail:` entry.

### Pass checklist

- `POST /` redirects to `/read`.
- The following `GET /read` returns `200 OK`.
- Both reads return the fresh value and have markers `true`.
- Reload produces a new request where both reads are empty and markers are `false`.
- No browser or server error occurs.

## TC-03 - Navigate away and return

**Purpose:** Prove that a consumed value does not return on a later server request and distinguish browser Back cache behavior.

**Important:** Leave **Disable cache** unchecked for this testcase.

### Part A: navigate away and request the reader again

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open DevTools and prepare Network with **Preserve log** checked.
4. Enter a fresh value such as `TC03-A-20260909-001`.
5. Select **Consume with ITempData**.
6. Click **Store and redirect**.
7. Confirm `/read` displays the value in both reads.
8. Write down its **Request UTC**.

**Evidence checkpoint - capture now:** Before clicking **Seed another message**, capture the `/read` page with the address bar, heading, both complete values, and original **Request UTC** visible. Save as `TC-03-01-populated-reader.png`.

9. Click **Seed another message** below the results.
10. Confirm the address bar returns to `/`.
11. Do not click **Store and redirect**.
12. Click **Consume** in the left navigation.
13. Confirm `/read` loads.
14. Confirm both reads now display `empty`.
15. Confirm both output markers are `false`.
16. Confirm **Request UTC** differs from the populated reader timestamp.

**Evidence checkpoint - capture now:** Before switching to Network, capture the newly navigated `/read` page. Include both `empty` results, the new **Request UTC**, and the address bar. Save as `TC-03-02-new-navigation-empty.png`.

17. Click the newest `read` row in Network and confirm it is a new `GET` with `200 OK`.

**Evidence checkpoint - capture now:** On **Network > Headers**, include the newest selected `read` row, at least one earlier `read` row, and **General** showing the new `/read` request, `GET`, and `200 OK`. Save as `TC-03-03-new-navigation-network.png`.

### Part B: test the browser Back button

1. Close all Incognito windows.
2. Open a new Incognito window with **Ctrl+Shift+N**.
3. Open `https://localhost:7171/`.
4. Open DevTools and enable **Preserve log**. Leave **Disable cache** unchecked.
5. Enter a second fresh value such as `TC03-B-20260909-001`.
6. Select **Consume with ITempData** and click **Store and redirect**.
7. Confirm the populated `/read` page and record its **Request UTC**.
8. Click **Seed another message** to return to `/`.
9. Click the **Clear network log** icon while remaining on `/`.
10. Click Chrome's **Back** arrow once.
11. Look at the Network list before doing anything else.
12. If no new `read` request appears and the old value/timestamp returns, record `Back restored cached document; no server request`.
13. If a new `read` request appears, click it and confirm the response is empty with a new timestamp.

**Evidence checkpoint - capture now:** Before pressing **Ctrl+R**, capture the page and Network list together. If Back restored cache, include the restored value, original **Request UTC**, and empty Network list. If Back made a request, include the empty result, new timestamp, and selected `read` row. Save as `TC-03-04-back-result.png`.

14. Click the page and press **Ctrl+R** to force a new request.
15. Confirm both reads are empty and markers are `false`.
16. Confirm Network now contains a new `GET /read` and Request UTC is new.

**Evidence checkpoint - capture now:** Keep Network visible with the forced reload `read` row selected. Include both empty page results, the new **Request UTC**, and the Network row showing the new `GET /read`. Save as `TC-03-05-forced-reload.png`.

17. Check Console and the server terminal for errors.

### Pass checklist

- Every new server request after consumption is empty.
- A restored old page is accepted only when Network proves no request occurred.
- A forced reload after Back is empty.
- No browser or server error occurs.

## TC-04 - Untouched page preserves value

**Purpose:** Prove that a page with no TempData access preserves the value and satisfy Must Hold 2.

**Test value:** `TC04-20260909-001` or a fresh equivalent.

### Prepare and submit

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Click **Message**, press **Ctrl+A**, and type the fresh value.
5. Click **Redirect target**.
6. Click **Do not touch TempData**.

**Evidence checkpoint - capture now:** Before submitting, capture the seed page with the address bar, heading, complete message, selected **Do not touch TempData** target, and submit button visible. Save as `TC-04-00-seed.png`.

7. Click **Store and redirect**.
8. Confirm the address bar ends with `/untouched`.

### Verify the untouched request

1. Confirm the heading says **TempData untouched**.
2. Confirm **Operation** says `No cascading value, parameter binding, read, peek, keep, or write`.
3. Record the displayed **Request UTC**.

**Evidence checkpoint - capture now:** Before opening Network or navigating, capture the `/untouched` page with the address bar, heading, complete **Operation** text, **Request UTC**, and **Continue to ordinary reader** button. Save as `TC-04-01-untouched.png`.

4. In Network, click the `untouched` row.
5. Confirm **Request Method** is `GET` and **Status Code** is `200 OK`.

**Evidence checkpoint - capture now:** On **Network > Headers**, include the selected `untouched` row and **General** showing `/untouched`, `GET`, and `200 OK`. Save as `TC-04-02-untouched-network.png`.

### Continue to the consuming reader

1. Do not click any navigation item or reload the untouched page.
2. Click **Continue to ordinary reader**.
3. Confirm the address bar ends with `/read`.
4. Confirm **First read** equals the fresh value.
5. Confirm **Second read** equals the fresh value.
6. Inspect `#first-read` and `#second-read` and confirm both markers are `true`.
7. Record the new **Request UTC**.

**Evidence checkpoint - capture now:** Before reloading, capture the `/read` page with the address bar, heading, both complete values, and new **Request UTC**. Save as `TC-04-03-reader-value.png`.

**Evidence checkpoint - capture now:** Before leaving **Elements**, show both complete output lines with IDs and `data-has-value="true"` beside the populated page. Save as `TC-04-04-reader-markup.png`.

8. In Network, click the newest `read` row and confirm `GET` with `200 OK`.

### Reload after consumption

1. Click the page and press **Ctrl+R** once.
2. Confirm both reads display `empty`.
3. Confirm both markers are now `false`.
4. Confirm Request UTC changed.
5. Confirm Network contains another `GET /read` with `200 OK`.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include the page's two `empty` results, changed **Request UTC**, and both output lines with markers `false`; save as `TC-04-05-reader-reload.png`. Second, switch to **Network > Headers**, select the reloaded `read` row, and include `/read`, `GET`, and `200 OK`; save as `TC-04-06-reader-reload-network.png`.

6. Check Console and the server terminal for errors.

### Pass checklist

- `/untouched` performs no TempData operation and returns `200 OK`.
- The following ordinary reader still receives the value in both reads.
- Reloading the ordinary reader is empty.
- No browser or server error occurs.

## TC-05 - Peek, reload, consume

**Purpose:** Prove that `Peek` does not mark the value for deletion and satisfy Must Hold 3.

**Test value:** `TC05-20260909-001` or a fresh equivalent.

### Seed the Peek flow

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Click **Message**, press **Ctrl+A**, and type the fresh value.
5. Click **Redirect target**.
6. Click **Peek**.
7. Click **Store and redirect**.
8. Confirm the address bar ends with `/peek`.
9. Confirm the heading says **Non-consuming reader**.
10. Confirm **Operation** displays `Peek("ValidationMessage")`.

### Verify the first Peek request

1. Confirm **Peek result** exactly equals the fresh value.
2. Record the first **Request UTC**.
3. Inspect `#peek-message`.
4. Confirm `data-has-value="true"` and copy its outer HTML into `evidence\TC-05-markup.txt` under `First GET /peek`.

**Evidence checkpoint - capture now:** Before reloading, keep **Elements** open with the complete `#peek-message` line selected. Include the `/peek` address, **Non-consuming reader** heading, `Peek("ValidationMessage")` operation, full result, first **Request UTC**, and `data-has-value="true"`. Save as `TC-05-01-first-peek.png`.

5. In Network, click the `peek` row and confirm it is a `GET` with `200 OK`.

**Evidence checkpoint - capture now:** On **Network > Headers**, keep the first `peek` row selected and include **General** showing `/peek`, `GET`, and `200 OK`. Save as `TC-05-02-first-peek-network.png`.

### Reload Peek and verify retention

1. Click **Reload peek reader**.
2. Confirm the URL remains `/peek`.
3. Confirm **Peek result** still exactly equals the fresh value.
4. Confirm the new **Request UTC** differs from the first timestamp.
5. Inspect `#peek-message` and confirm its marker remains `true`.
6. Copy the element into `evidence\TC-05-markup.txt` under `Second GET /peek`.
7. In Network, click the newest `peek` row and confirm it is another `GET` with `200 OK`.

**Evidence checkpoint - capture now:** Take two images before continuing to the ordinary reader. First, on **Elements**, include the unchanged Peek value, new **Request UTC**, and selected `#peek-message` line with marker `true`; save as `TC-05-03-second-peek.png`. Second, switch to **Network**, show both `peek` rows with the newest selected and `200 OK`; save as `TC-05-03-second-peek-network.png`.

### Consume the retained value

1. Click **Continue to ordinary reader**.
2. Confirm the address bar ends with `/read`.
3. Confirm both **First read** and **Second read** exactly equal the fresh value.
4. Inspect `#first-read` and `#second-read` and confirm both markers are `true`.

**Evidence checkpoint - capture now:** Before reloading `/read`, include the address bar, heading, both full values, **Request UTC**, and both output lines in **Elements** showing markers `true`. Save as `TC-05-04-consuming-read.png`.

5. Click the page and press **Ctrl+R** once.
6. Confirm both reads now display `empty`.
7. Confirm both markers are now `false`.
8. Confirm Request UTC changed and Network contains a new `GET /read` with `200 OK`.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include both reloaded `empty` results, new **Request UTC**, and both output lines with markers `false`; save as `TC-05-05-after-consume.png`. Second, switch to **Network > Headers**, select the newest `read` row, and include `/read`, `GET`, and `200 OK`; save as `TC-05-05-after-consume-network.png`.

9. Check Console and the server terminal for errors.

### Pass checklist

- Both Peek requests return the same value with marker `true`.
- Request UTC changes, proving the second Peek is a new request.
- The next ordinary reader receives the value.
- Reloading the ordinary reader is empty.
- No browser or server error occurs.

## TC-06 - Read and keep exactly once

**Purpose:** Prove that `Get` followed by `Keep(key)` retains an already-read key and satisfy Must Hold 4.

**Test value:** `TC06-20260909-001` or a fresh equivalent.

### Seed the read-and-keep flow

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Enter the fresh value in **Message**.
5. Open **Redirect target** and click **Read and keep once**.
6. Click **Store and redirect**.
7. Confirm the address bar ends with `/keep-once`.
8. Confirm the heading says **Read and keep once**.

### Verify the keep request

1. Confirm **Operation** shows `Get("ValidationMessage")`, then `Keep("ValidationMessage")`.
2. Confirm **Read result** exactly equals the fresh value.
3. Record **Request UTC**.
4. Inspect `#keep-message` and confirm `data-has-value="true"`.
5. Copy its outer HTML into `evidence\TC-06-markup.txt` under `GET /keep-once`.

**Evidence checkpoint - capture now:** Before any reload or navigation, keep `#keep-message` selected in **Elements**. Include the `/keep-once` address, heading, complete operation, full result, **Request UTC**, and `data-has-value="true"`. Save as `TC-06-01-keep-once.png`.

6. In Network, click the `keep-once` row and confirm `GET` with `200 OK`.
7. Do not reload this page. Reloading `/keep-once` would perform another read-and-keep request and change the intended sequence.

### Consume on the next request

1. Click **Continue to ordinary reader**.
2. Confirm the address bar ends with `/read`.
3. Confirm both ordinary reads exactly equal the fresh value.
4. Inspect `#first-read` and `#second-read` and confirm both markers are `true`.

**Evidence checkpoint - capture now:** Before reloading, include the populated `/read` page, both complete output lines with markers `true`, and **Request UTC**. Save as `TC-06-02-ordinary-reader.png`.

5. Click the page and press **Ctrl+R** once.
6. Confirm both reads now display `empty`.
7. Confirm both markers are `false` and Request UTC changed.
8. In Network, verify the order is `GET /keep-once`, `GET /read`, then reload `GET /read`.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include both reloaded `empty` results, new **Request UTC**, and both output lines with markers `false`; save as `TC-06-03-reader-reload.png`. Second, switch to **Network** and include the ordered successful rows for `/keep-once`, initial `/read`, and reloaded `/read`; save as `TC-06-03-reader-reload-network.png`.

9. Check Console and the server terminal for errors.

### Pass checklist

- The keep page reads and displays the value once.
- The immediately following ordinary reader still receives the value.
- Reloading the ordinary reader is empty.
- The request order contains no unexpected redirect or failed request.

## TC-07 - Peek then keep in one request

**Purpose:** Prove that `Peek` followed by `Keep(key)` against the same key retains the value without error.

**Test value:** `TC07-20260909-001` or a fresh equivalent.

### Seed and verify Peek-then-Keep

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Enter the fresh value in **Message**.
5. Open **Redirect target** and click **Peek, then keep**.
6. Click **Store and redirect**.
7. Confirm the address bar ends with `/peek-keep`.
8. Confirm the heading says **Peek, then keep the same key**.
9. Confirm **Operation** lists `Peek("ValidationMessage")`, then `Keep("ValidationMessage")`.
10. Confirm **Peek result** exactly equals the fresh value.
11. Record **Request UTC**.
12. Inspect `#peek-keep-message` and confirm `data-has-value="true"`.
13. Copy its outer HTML into `evidence\TC-07-markup.txt`.
14. In Network, click the `peek-keep` row and confirm `GET` with `200 OK`.

**Evidence checkpoint - capture now:** Take two images before clicking **Continue to ordinary reader**. First, on **Elements**, include the `/peek-keep` address, heading, complete operation, full result, **Request UTC**, and selected `#peek-keep-message` line with marker `true`; save as `TC-07-01-peek-keep.png`. Second, switch to **Network > Headers**, select the `peek-keep` row, and include `/peek-keep`, `GET`, and `200 OK`; save as `TC-07-01-peek-keep-network.png`.

### Consume and verify removal

1. Click **Continue to ordinary reader**.
2. Confirm `/read` loads in a new request.
3. Confirm both reads exactly equal the fresh value.
4. Confirm `#first-read` and `#second-read` both have markers `true`.

**Evidence checkpoint - capture now:** Before reloading, include the populated `/read` page, both complete output lines with markers `true`, and **Request UTC**. Save as `TC-07-02-ordinary-reader.png`.

5. Click the page and press **Ctrl+R** once.
6. Confirm both reads are empty and both markers are `false`.
7. Confirm Request UTC changed.
8. In Network, verify successful requests to `/peek-keep`, `/read`, and the reloaded `/read` in that order.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include both reloaded `empty` results, new **Request UTC**, and both output lines with markers `false`; save as `TC-07-03-reader-reload.png`. Second, switch to **Network** and include the ordered successful rows for `/peek-keep`, initial `/read`, and reloaded `/read`; save as `TC-07-03-reader-reload-network.png`.

9. Check Console and the server terminal for errors.

### Pass checklist

- Peek-then-Keep displays the submitted value with marker `true`.
- The next ordinary reader receives the retained value.
- Reloading the ordinary reader is empty.
- No exception, failed request, or browser error occurs.

## TC-08 - Read the same key twice in one request

**Purpose:** Prove that an ordinary read schedules deletion for request completion rather than deleting the key immediately.

**Test value:** `TC08-20260909-001` or a fresh equivalent.

### Understand the expected request sequence

This testcase contains two different HTTP stages:

1. The browser sends one `POST /` when **Store and redirect** is selected. The server stores the message and redirects to `/read`.
2. The browser follows that redirect with one document `GET /read`.
3. While the server renders that one `GET /read` response, the page calls `Get("ValidationMessage")` twice.
4. The first `Get` marks the key for deletion when the request finishes; it does not remove the value immediately. Therefore, the second `Get` in the same request must return the same value.
5. Reloading the page later creates a second `GET /read`. The deletion from the first GET has completed, so both reads must then be empty.

Network can also contain CSS, JavaScript, favicon, or DevTools requests. When this testcase says **one GET**, it means one document request whose path is `/read`, not one row in the entire Network panel.

### Prepare and submit the value

1. Close every Chrome Incognito window.
2. Open a new Incognito window with **Ctrl+Shift+N**.
3. Open `https://localhost:7171/`.
4. Press **F12** and click **Network**.
5. Check **Preserve log**.
6. Leave **Disable cache** unchecked.
7. Click the **Clear network log** icon so requests from earlier tests are removed.
8. Click the **Message** textbox, press **Ctrl+A**, and enter the fresh test value.
9. Open **Redirect target** and select **Consume with ITempData**.
10. Confirm the textbox contains the exact test value and the target is correct.
11. Click **Store and redirect** once.
12. Wait until the address bar ends with `/read` and the heading says **Ordinary consuming reader**.
13. Do not reload, navigate, or use the Back button until all initial-response evidence below has been captured.

### Verify both reads in the initial GET response

1. Find the **First read** row on the page.
2. Confirm **First read** exactly equals the submitted test value.
3. Find the **Second read** row.
4. Confirm **Second read** exactly equals the same test value.
5. Confirm the page contains one **Request UTC** row beneath the two results.
6. Record that timestamp as `Initial GET /read Request UTC`.

**Evidence checkpoint - capture now:** Capture the rendered page before opening another route or reloading. Include the address bar ending in `/read`, **Ordinary consuming reader** heading, complete **First read** value, complete **Second read** value, and **Request UTC**. Save as `TC-08-01-two-reads-same-request.png`.

### Verify the initial output markup

1. Click the **Elements** tab in DevTools.
2. Press **Ctrl+F**, enter `#first-read`, and press **Enter**.
3. Confirm the selected element is `output#first-read`.
4. Confirm it contains the exact submitted value.
5. Confirm it has `data-has-value="true"`.
6. Right-click the element, click **Copy > Copy outerHTML**, and paste it into `evidence\TC-08-markup.txt` under `Initial GET /read - first read`.
7. Press **Ctrl+F**, enter `#second-read`, and press **Enter**.
8. Confirm the selected element is `output#second-read`.
9. Confirm it contains the same submitted value and has `data-has-value="true"`.
10. Copy its outer HTML into the same file under `Initial GET /read - second read`.

**Evidence checkpoint - capture now:** On **Elements**, show the populated `/read` page beside the output markup. Include both result rows and the complete selected output line with its `id`, submitted value, and `data-has-value="true"`. If both output lines do not fit in one view, save a second image as `TC-08-01-two-reads-same-request-part-2.png`.

### Verify that one GET produced both reads

1. Click the **Network** tab.
2. Locate the root request whose **Method** is `POST`.
3. Confirm that POST appears before the `read` document request.
4. Locate the first document row named `read` whose **Method** is `GET`.
5. Ignore rows for CSS, JavaScript, images, favicon, or browser extensions.
6. Click the `read` row and open **Headers**.
7. Under **General**, confirm **Request URL** is `https://localhost:7171/read`.
8. Confirm **Request Method** is `GET`.
9. Confirm **Status Code** is `200 OK`.
10. Confirm there is only one document `GET /read` between the seed POST and this point. Do not count the two displayed TempData reads as separate Network requests; they ran on the server while this one response was rendered.

**Evidence checkpoint - capture now:** On **Network > Headers**, include the seed POST row followed by the single initial `read` document row. Keep `read` selected and show **General** with `/read`, `GET`, and `200 OK`. Save as `TC-08-02-one-read-request.png`.

### Reload and verify deletion on the next request

1. Write down the current number of document `GET /read` rows in Network. At this point it should be one.
2. Click the rendered page area above DevTools.
3. Press **Ctrl+R** exactly once.
4. Wait for `/read` to finish loading.
5. Confirm **First read** now displays `empty`.
6. Confirm **Second read** now displays `empty`.
7. Confirm the new **Request UTC** differs from `Initial GET /read Request UTC`.
8. In **Elements**, inspect `#first-read` and confirm `data-has-value="false"`.
9. Copy its outer HTML into `evidence\TC-08-markup.txt` under `Reload GET /read - first read`.
10. Inspect `#second-read` and confirm `data-has-value="false"`.
11. Copy its outer HTML under `Reload GET /read - second read`.

**Evidence checkpoint - capture now:** On **Elements**, include both `empty` result rows, the changed **Request UTC**, and the complete selected output line showing `data-has-value="false"`. If both output lines do not fit, save `TC-08-03-next-request-part-2.png` for the second element. Save the first image as `TC-08-03-next-request.png`.

12. Click **Network**.
13. Confirm a second document `GET /read` row now appears after the initial one.
14. Select the newest `read` row.
15. Under **Headers > General**, confirm `/read`, `GET`, and `200 OK`.

**Evidence checkpoint - capture now:** Include both initial and reloaded `read` document rows in the Network list. Keep the second row selected and show **General** with `/read`, `GET`, and `200 OK`. Save as `TC-08-03-next-request-network.png`.

16. Click **Console** and confirm there are no red application errors.
17. Check the server PowerShell window and confirm there is no exception or `fail:` entry for this flow.

### Pass checklist

- The initial Network sequence contains the seed POST followed by one document `GET /read`.
- **First read** and **Second read** both return the exact value in that one initial GET response.
- Both initial output elements have `data-has-value="true"`.
- One reload adds a second document `GET /read` with a different Request UTC.
- Both reloaded outputs display `empty` and have `data-has-value="false"`.
- No browser or server error occurs.

## TC-09 - Bound property cleared

**Purpose:** Prove the one-time `[SupplyParameterFromTempData]` pattern and satisfy Must Hold 5.

**Test value:** `TC09-20260909-001` or a fresh equivalent.

### Seed the bound-and-clear flow

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Enter the fresh value in **Message**.
5. Open **Redirect target** and click **Bound property, then clear**.
6. Click **Store and redirect**.
7. Confirm the address bar ends with `/read-bound-clear`.
8. Confirm the heading says **Bound reader that clears the property**.

### Verify the first bound request

1. Confirm **Displayed message** exactly equals the fresh value.
2. Confirm **Property after copy** displays `null`.
3. Record **Request UTC**.

**Evidence checkpoint - capture now:** Before opening **Elements**, capture the `/read-bound-clear` page with the address bar, heading, complete displayed value, `Property after copy: null`, and **Request UTC**. Save as `TC-09-01-bound-value.png`.

4. Inspect `#bound-clear-message`.
5. Confirm `data-has-value="true"`.
6. Copy the output outer HTML into `evidence\TC-09-markup.txt` under `Initial GET /read-bound-clear`.

**Evidence checkpoint - capture now:** Keep `#bound-clear-message` selected in **Elements** and include the page's value and `null` row beside the complete output line showing `data-has-value="true"`. Save as `TC-09-02-bound-markup.png`.

7. In Elements, search for the visible text `Property after copy` and confirm the DOM row contains `null`.
8. In Network, click the `read-bound-clear` row and confirm `GET` with `200 OK`.

### Reload and verify no writeback

1. Click the page and press **Ctrl+R** once.
2. Confirm **Displayed message** now displays `empty`.
3. Confirm **Property after copy** still displays `null`.
4. Confirm Request UTC changed.
5. Inspect `#bound-clear-message` and confirm its marker is now `false`.
6. Copy the element into `evidence\TC-09-markup.txt` under `Reload GET /read-bound-clear`.
7. Confirm Network contains a new `GET /read-bound-clear` with `200 OK`.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include the reloaded `empty` result, `Property after copy: null`, new **Request UTC**, and selected output line with marker `false`; save as `TC-09-03-bound-reload.png`. Second, switch to **Network > Headers**, select the newest `read-bound-clear` row, and include its `GET` and `200 OK`; save as `TC-09-03-bound-reload-network.png`.

8. Check Console for binding or serialization errors.
9. Check the server terminal for exceptions or `fail:` entries.

### Pass checklist

- The first bound request displays the submitted value with marker `true`.
- The copied bound property is visibly `null` before request completion.
- Reload is empty with marker `false` and the property remains `null`.
- No binding, serialization, browser, or server error occurs.

## TC-10 - Cascading and bound-reader parity

**Purpose:** Compare the cascading `ITempData` reader and bound-property reader with exactly the same input.

**Comparison value:** `TC10-SAME-20260909-001` or a fresh equivalent. Use the identical string in both parts.

### Part A: cascading `ITempData`

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Enter the comparison value in **Message**.
5. Select **Consume with ITempData**.
6. Click **Store and redirect**.
7. Confirm `/read` displays the comparison value in both reads.
8. Inspect both output elements and confirm both markers are `true`.
9. Copy both elements into `evidence\TC-10-markup.txt` under `Part A initial`.

**Evidence checkpoint - capture now:** Before reloading Part A, include the `/read` page, both full comparison values, **Request UTC**, and both output lines with markers `true`. Save as `TC-10-01-cascading-first.png`.

10. Press **Ctrl+R** once.
11. Confirm both reads are empty with markers `false` and Request UTC changed.

**Evidence checkpoint - capture now:** Before returning to the seed page, include both Part A `empty` results, changed **Request UTC**, and both output lines with markers `false`. Save as `TC-10-02-cascading-reload.png`.

### Part B: bound property cleared

1. Click **Seed another message**.
2. Confirm the address bar ends with `/`.
3. Replace the generated message with exactly the same comparison value used in Part A.
4. Select **Bound property, then clear**.
5. Click **Store and redirect**.
6. Confirm `/read-bound-clear` loads.
7. Confirm **Displayed message** exactly matches the Part A value, including capitalization and punctuation.
8. Confirm **Property after copy** displays `null`.
9. Inspect `#bound-clear-message` and confirm its marker is `true`.
10. Copy it into `evidence\TC-10-markup.txt` under `Part B initial`.

**Evidence checkpoint - capture now:** Before reloading Part B, include the `/read-bound-clear` address, complete comparison value, `Property after copy: null`, **Request UTC**, and `#bound-clear-message` with marker `true`. Save as `TC-10-03-bound-first.png`.

11. Press **Ctrl+R** once.
12. Confirm **Displayed message** is empty and its marker is `false`.
13. Confirm Request UTC changed.

**Evidence checkpoint - capture now:** Before opening the comparison text file, include the Part B `empty` result, `Property after copy: null`, changed **Request UTC**, and output line with marker `false`. Save as `TC-10-04-bound-reload.png`.

### Compare the two parts

1. Open `evidence\TC-10-markup.txt`.
2. Compare the text inside the Part A and Part B initial output elements character for character.
3. Confirm both contain the same comparison value.
4. Confirm both initial requests had marker `true`.
5. Confirm both reload requests had marker `false`.
6. Check Console and the server terminal for errors.

### Pass checklist

- Both implementations display identical input once.
- Both initial markers are `true`.
- Both implementations are empty on their respective reload request.
- No browser or server error occurs.

## TC-11 - Bound property left untouched

**Purpose:** Observe request-end writeback when `[SupplyParameterFromTempData]` supplies a property that the page does not change.

**Test value:** `TC11-20260909-001` or a fresh equivalent.

### Seed the untouched bound reader

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Enter the fresh value in **Message**.
5. Select **Bound property, untouched**.
6. Click **Store and redirect**.
7. Confirm the address bar ends with `/read-bound-untouched`.
8. Confirm the heading says **Bound reader that leaves the property untouched**.

### Verify the initial request and two reloads

1. Confirm **Displayed message** exactly equals the fresh value.
2. Confirm **Writeback value** exactly equals the fresh value.
3. Inspect `#bound-untouched-message` and `#bound-writeback-value` and confirm both markers are `true`.
4. Record the first **Request UTC**.

**Evidence checkpoint - capture now:** Before the first reload, include the `/read-bound-untouched` address, heading, both complete values, first **Request UTC**, and both output lines with markers `true`. Save as `TC-11-01-initial.png`.

5. Copy both output elements into `evidence\TC-11-markup.txt` under `Initial GET /read-bound-untouched`.
6. Click **Reload unchanged reader**.
7. Confirm both displayed values still equal the fresh value.
8. Confirm both markers remain `true`.
9. Confirm Request UTC changed and Network contains a new successful `GET /read-bound-untouched`.

**Evidence checkpoint - capture now:** Take two images before the second reload. First, on **Elements**, include both unchanged values, second **Request UTC**, and both output lines with markers `true`; save as `TC-11-02-reload-one.png`. Second, switch to **Network > Headers**, select the newest `read-bound-untouched` row, and include its `GET` and `200 OK`; save as `TC-11-02-reload-one-network.png`.

10. Copy both output elements into `evidence\TC-11-markup.txt` under `First reload GET /read-bound-untouched`.
11. Click **Reload unchanged reader** a second time.
12. Confirm both values and both `true` markers remain unchanged.
13. Confirm Request UTC changes again.

**Evidence checkpoint - capture now:** Take two images before clicking **Clear with bound reader**. First, on **Elements**, include both unchanged values, third **Request UTC**, and both output lines with markers `true`; save as `TC-11-03-reload-two.png`. Second, switch to **Network** and include all three successful `read-bound-untouched` rows, with the newest selected; save as `TC-11-03-reload-two-network.png`.

14. Copy both output elements into `evidence\TC-11-markup.txt` under `Second reload GET /read-bound-untouched`.

### Clear the retained value

1. Click **Clear with bound reader**.
2. Confirm the address bar ends with `/read-bound-clear`.
3. Confirm **Displayed message** still equals the fresh value on this request.
4. Confirm **Property after copy** displays `null`.
5. Confirm `#bound-clear-message` has marker `true`.

**Evidence checkpoint - capture now:** Before reloading the clearing reader, include the `/read-bound-clear` address, complete retained value, `Property after copy: null`, **Request UTC**, and selected output line with marker `true`. Save as `TC-11-04-clearing-reader.png`.

6. Click the page and press **Ctrl+R** once.
7. Confirm **Displayed message** now displays `empty`.
8. Confirm `#bound-clear-message` now has marker `false`.
9. Confirm Request UTC changed.

**Evidence checkpoint - capture now:** Include the reloaded clearing page's `empty` result, `Property after copy: null`, changed **Request UTC**, and output line with marker `false`. Save as `TC-11-05-after-clear.png`.

10. Check Console and the server terminal for errors.

### Pass checklist

- Initial, first reload, and second reload all show the value in both bound outputs.
- Every reload has a new Request UTC and successful Network request.
- The clearing reader shows the value once and changes the property to `null`.
- Reloading the clearing reader is empty.
- No binding, serialization, browser, or server error occurs.

## TC-12 - Write and read before redirect

**Purpose:** Prove that a newly written value is visible twice within the same POST request without a redirect.

**Test value:** `TC12-20260909-001` or a fresh equivalent.

### Submit on the same-request page

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/same-request`.
3. Confirm the heading says **Write and read in the same request**.
4. Open and prepare DevTools Network.
5. Click **Message**.
6. Press **Ctrl+A** and type the fresh value.
7. Click the **Clear network log** icon.
8. Click **Write and read twice**.
9. Wait for the response to finish.
10. Confirm the address bar still ends with `/same-request`.
11. Confirm the browser did not navigate to `/read`.

### Verify the POST and both reads

1. In Network, click the row whose method is `POST` and name is `same-request`.
2. Open **Headers > General**.
3. Confirm **Request URL** is `https://localhost:7171/same-request`.
4. Confirm **Request Method** is `POST`.
5. Confirm **Status Code** is a successful response. The expected result is `200 OK`.
6. Confirm there is no redirect `Location` header to `/read`.

**Evidence checkpoint - capture now:** Keep the `same-request` POST row selected on **Network > Headers**. Include the request row, **General** showing `/same-request`, `POST`, and `200 OK`, and the Response Headers area showing no redirect `Location`. Save as `TC-12-02-post-network.png`.

7. Look at the page.
8. Confirm **First read** exactly equals the submitted value.
9. Confirm **Second read** exactly equals the same value.
10. Record the one displayed **Request UTC** value.
11. Inspect `#same-request-first-read` and confirm its marker is `true`.
12. Copy its outer HTML into `evidence\TC-12-markup.txt` under `POST /same-request`.
13. Inspect `#same-request-second-read`, confirm `true`, and copy it under the same label.

**Evidence checkpoint - capture now:** Before clicking **Check the next request**, include the `/same-request` address, heading, both complete values, one **Request UTC**, and both output lines in **Elements** with markers `true`. Save as `TC-12-01-same-request.png`.

### Check the next request

1. Click **Check the next request**.
2. Confirm the address bar ends with `/read`.
3. Confirm Network contains a new `GET /read` with `200 OK`.
4. Confirm both ordinary reads display `empty`.
5. Inspect `#first-read` and `#second-read` and confirm both markers are `false`.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include the `/read` address, both `empty` results, **Request UTC**, and both output lines with markers `false`; save as `TC-12-03-next-request.png`. Second, switch to **Network > Headers**, select the new `read` row, and include `/read`, `GET`, and `200 OK`; save as `TC-12-03-next-request-network.png`.

6. Check Console and the server terminal for errors.

### Pass checklist

- The form produces a successful `POST /same-request` without redirecting.
- Both reads in that POST response return the fresh value with markers `true`.
- The following `GET /read` is empty with markers `false`.
- No browser or server error occurs.

## TC-13 - Submit then direct return to writer

**Purpose:** Prove that rendering the writer page neither revives consumed TempData nor stores its generated textbox value.

**Test value:** `TC13-20260909-001` or a fresh equivalent.

### Consume one submitted value

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Replace **Message** with the fresh value.
5. Select **Consume with ITempData**.
6. Click **Store and redirect**.
7. Confirm `/read` displays the fresh value in both reads.
8. Record Request UTC.

**Evidence checkpoint - capture now:** Before clicking **Seed another message**, include the `/read` address, heading, both complete submitted values, both `true` markers in **Elements**, and **Request UTC**. Save as `TC-13-01-consumed.png`.

### Visit the writer without submitting

1. Click **Seed another message**.
2. Confirm the address bar ends with `/`.
3. Confirm the **Message** textbox contains a newly generated value beginning with `message-`.
4. Copy that generated value into your notes as `Writer default`. It must differ from the submitted test value.
5. Do not click **Store and redirect**.
6. In Network, click the newest root document request.
7. Confirm it is `GET https://localhost:7171/` with `200 OK`.

**Evidence checkpoint - capture now:** Before clicking **Consume**, include the root address, seed-page heading, complete generated **Writer default** value, untouched submit button, and selected root request on **Network > Headers** showing `GET` and `200 OK`. Save as `TC-13-02-writer-get-only.png`.

### Return directly to the reader

1. Click **Consume** in the left navigation. If the navigation is collapsed, click its menu toggle first and then click **Consume**.
2. Confirm the address bar ends with `/read`.
3. Confirm both reads display `empty`.
4. Confirm `#first-read` and `#second-read` both have markers `false`.
5. Confirm neither the submitted value nor the recorded writer default appears anywhere in the two results.
6. Confirm Request UTC is newer than the consumed request.
7. Confirm Network contains a new `GET /read` with `200 OK`.

**Evidence checkpoint - capture now:** Include the final `/read` address, both `empty` results, both output lines with markers `false`, new **Request UTC**, and selected successful `GET /read` row. Save as `TC-13-03-reader-empty.png`.

8. Check Console and the server terminal for errors.

### Pass checklist

- The original value is consumed once.
- Opening `/` performs only a GET and does not submit the generated textbox value.
- The next reader contains neither the consumed value nor the writer default.
- Both markers are `false` and no error occurs.

## TC-14 - Empty string value

**Purpose:** Distinguish a present empty string from a missing TempData key.

### Submit a blank value

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Click the **Message** textbox.
5. Press **Ctrl+A**.
6. Press **Backspace** once.
7. Confirm the textbox is completely blank; do not type a space.
8. Select **Consume with ITempData**.

**Evidence checkpoint - capture now:** Before submitting, capture the seed page with the address bar, heading, visibly blank **Message** textbox, selected **Consume with ITempData** target, and **Store and redirect** button. Save as `TC-14-00-empty-input.png`.

9. Click **Store and redirect**.
10. Confirm the address bar ends with `/read`.

### Prove the empty string is present

1. Observe that both result rows visually show `empty`.
2. Remember that CSS displays this word for an empty `<output>`; the visible word alone cannot prove whether the key exists.
3. Inspect `#first-read`.
4. Confirm the element contains no text between its opening and closing tags.
5. Confirm its `data-has-value` is `true`.
6. Copy its outer HTML into `evidence\TC-14-present-empty-markup.txt`.
7. Inspect `#second-read`.
8. Confirm it also contains no text and has marker `true`.
9. Copy it into the same file.
10. Record Request UTC.

**Evidence checkpoint - capture now:** Before reloading, include the `/read` page showing `empty` for both rows and first **Request UTC**, plus both complete empty output lines in **Elements** showing `data-has-value="true"`. Save as `TC-14-01-present-empty.png`.

### Prove the key is missing on reload

1. Click the page and press **Ctrl+R** once.
2. Observe that both rows still visually show `empty`.
3. Confirm Request UTC changed.
4. Inspect `#first-read` and `#second-read` again.
5. Confirm both `data-has-value` attributes are now `false`.
6. Copy both elements into `evidence\TC-14-missing-markup.txt`.
7. Confirm Network contains a new `GET /read` with `200 OK`.

**Evidence checkpoint - capture now:** Include the visually identical two `empty` rows, changed **Request UTC**, both complete output lines now showing `data-has-value="false"`, and newest successful `GET /read` row. Save as `TC-14-02-missing-after-reload.png`.

8. Check Console and the server terminal for errors.

### Pass checklist

- The initial response contains empty output text with markers `true`.
- The reloaded response looks similar visually but has markers `false`.
- Request UTC proves the reload is a new request.
- No browser or server error occurs.

## TC-15 - HTML-sensitive characters

**Purpose:** Prove exact string round-trip and safe Razor HTML encoding.

**Exact test value:**

```text
<strong>& "quoted"</strong>
```

### Submit the exact value

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Click **Message**, press **Ctrl+A**, and type or paste the exact test value above.
5. Confirm the value includes both angle brackets, the ampersand, both double quotes, and the closing tag.
6. Select **Consume with ITempData**.
7. Click **Store and redirect**.
8. Confirm `/read` loads.

### Verify visible text and markup

1. Confirm both reads visibly show the literal string `<strong>& "quoted"</strong>`.
2. Confirm the words are not rendered in bold.
3. Inspect `#first-read` in Elements.
4. Confirm there is no child `<strong>` element inside the output.
5. Confirm copied outer HTML contains encoded text such as `&lt;strong&gt;` and `&amp;`.
6. Confirm its marker is `true`.
7. Copy both output elements into `evidence\TC-15-markup.txt`.

**Evidence checkpoint - capture now:** Before switching to Console, include the `/read` page showing the literal non-bold string in both rows and **Request UTC**, plus the selected output markup in **Elements** showing encoded entities, no nested `<strong>` node, and marker `true`. Save as `TC-15-01-encoded-value.png`.

### Verify with the Console

1. Click the **Console** tab.
2. Type the following expression. If Chrome blocks pasting, type it manually:

   ```javascript
   document.querySelector('#first-read')?.querySelectorAll('strong').length
   ```

3. Press **Enter**.
4. Confirm the result is `0`.
5. Confirm there are no red Console errors.

**Evidence checkpoint - capture now:** Before reloading, keep **Console** open and include the complete `querySelectorAll('strong').length` expression, returned `0`, the Console error counter/no-error state, and enough of the page to show the literal non-bold result. Save as `TC-15-02-console-result.png`.

### Verify one-time removal

1. Click the page and press **Ctrl+R** once.
2. Confirm both reads are empty and both markers are `false`.
3. Confirm Request UTC changed.

**Evidence checkpoint - capture now:** Include the reloaded `/read` page with both `empty` results and changed **Request UTC**, plus both output lines in **Elements** showing markers `false`. Save as `TC-15-03-after-reload.png`.

4. Check the server terminal for errors.

### Pass checklist

- Both reads display the exact literal test string once.
- The browser creates zero nested `strong` elements from the value.
- The copied markup contains HTML entities and both markers are initially `true`.
- Reload is empty with markers `false`.
- No browser security, script, or server error occurs.

## TC-16 - Unicode message

**Purpose:** Prove that the cookie-backed provider preserves Unicode string content.

**Exact test value:** Use the actual characters below, not escape sequences.

```text
TC16 - cafe - Japanese: 日本 - Arabic: مرحبا
```

### Submit the Unicode value

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Open `evidence\TC-16-input.txt` in VS Code or Notepad.
5. Put the exact test value in that file so there is a comparison source.

**Evidence checkpoint - capture now:** Before copying or submitting the value, save `evidence\TC-16-input.txt` and confirm its only test-content line is exactly `TC16 - cafe - Japanese: 日本 - Arabic: مرحبا`. This text file is the source evidence; no screenshot is required for this checkpoint.

6. Copy the test value.
7. Return to Chrome.
8. Click **Message**, press **Ctrl+A**, and paste the value.
9. Select **Consume with ITempData**.
10. Click **Store and redirect**.
11. Confirm `/read` loads.

### Compare the returned text

1. Compare **First read** with `evidence\TC-16-input.txt` character by character.
2. Confirm **Second read** is identical to the same source.
3. Check the order of `日本`.
4. Check the Arabic text is `مرحبا` with no missing or separated characters.
5. Check every space, colon, and hyphen.
6. Confirm neither result contains the replacement character `�`.
7. Inspect `#first-read` and `#second-read` and confirm both markers are `true`.
8. Copy both elements into `evidence\TC-16-markup.txt`.
9. Confirm Network shows `GET /read` with `200 OK`.

**Evidence checkpoint - capture now:** Before reloading, place the saved input file beside the browser if practical. Include both complete Unicode results, **Request UTC**, both output lines with markers `true`, and the successful `GET /read` row. Ensure `日本`, `مرحبا`, punctuation, and spacing are readable. Save as `TC-16-01-unicode-read.png`.

### Verify one-time removal

1. Click the page and press **Ctrl+R** once.
2. Confirm both reads display `empty`.
3. Confirm both markers are `false` and Request UTC changed.

**Evidence checkpoint - capture now:** Include the reloaded `/read` page with both `empty` results and changed **Request UTC**, plus both output lines with markers `false`. Save as `TC-16-02-after-reload.png`.

4. Check Console and the server terminal for encoding or serialization errors.

### Pass checklist

- Both initial reads exactly match the saved Unicode source.
- No character is replaced, reordered, or lost.
- Both initial markers are `true`.
- Reload is empty with markers `false`.
- No encoding, serialization, browser, or server error occurs.

## TC-17 - Replace a retained value

**Purpose:** Prove that a new write to the same key replaces retained content rather than exposing stale data.

**Value A:** `TC17-A-20260909-001`

**Value B:** `TC17-B-20260909-001`

Use fresh equivalents and make sure A and B are visibly different.

### Retain value A

1. Start with a clean Incognito session.
2. Open `https://localhost:7171/`.
3. Open and prepare DevTools Network.
4. Enter value A in **Message**.
5. Select **Bound property, untouched**.
6. Click **Store and redirect**.
7. Confirm `/read-bound-untouched` loads.
8. Confirm both **Displayed message** and **Writeback value** equal A.
9. Record the first Request UTC.
10. Click **Reload unchanged reader**.
11. Confirm both values still equal A and Request UTC changed.

**Evidence checkpoint - capture now:** Take two images before navigating to the seed page. First, on **Elements**, include both retained A values, changed **Request UTC**, and both output lines with markers `true`; save as `TC-17-01-retained-A.png`. Second, switch to **Network > Headers**, select the newest `read-bound-untouched` row, and include its `GET` and `200 OK`; save as `TC-17-01-retained-A-network.png`.

### Replace A with B

1. Click **Seed another message**.
2. Confirm the address bar ends with `/`.
3. Replace the generated **Message** value with value B.
4. Confirm the textbox contains B and does not contain A.
5. Select **Consume with ITempData**.
6. Click **Store and redirect**.
7. Confirm `/read` loads.
8. Confirm **First read** exactly equals B.
9. Confirm **Second read** exactly equals B.
10. Inspect both outputs and confirm both markers are `true`.

**Evidence checkpoint - capture now:** Before switching to Console, include the `/read` address, both complete B values, **Request UTC**, and both output lines with markers `true`. Ensure no A text is visible anywhere in the captured page or markup. Save as `TC-17-02-replaced-with-B.png`.

11. Click the **Console** tab.
12. Type the following expression, replacing the sample A if you used another identifier:

   ```javascript
   document.body.innerText.includes('TC17-A-20260909-001')
   ```

13. Press **Enter** and confirm the result is `false`.
14. Inspect both output elements and confirm A is absent from their markup.

**Evidence checkpoint - capture now:** Keep **Console** open with the complete `innerText.includes(...)` expression and returned `false` visible. Include the populated B results above DevTools. Save as `TC-17-03-A-absent-console.png`.

### Consume B and check for stale data

1. Click the page and press **Ctrl+R** once.
2. Confirm both reads display `empty`.
3. Confirm both markers are `false`.
4. Confirm neither A nor B appears in either result.
5. Confirm Request UTC changed.
6. In Network, verify the successful order: retained A request, root writer request, B POST/redirect, initial B reader, and reader reload.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include both final `empty` results, changed **Request UTC**, and both output lines with markers `false`; save as `TC-17-04-after-B-consumed.png`. Second, switch to **Network** and include the ordered rows ending in the reload `GET /read`; save as `TC-17-04-after-B-consumed-network.png`.

7. Check Console and the server terminal for errors.

### Pass checklist

- A survives the unchanged bound-reader reload.
- The fresh write displays only B in both ordinary reads.
- A is absent from rendered text and output markup after replacement.
- Reload after consuming B is empty and neither value reappears.
- No browser or server error occurs.

## TC-18 - Browser-session isolation

**Purpose:** Prove that cookie-backed TempData belongs to one browser cookie jar and is not shared with another.

**Session A:** Chrome Incognito.

**Session B:** Microsoft Edge InPrivate.

**Test value:** `TC18-A-20260909-001` or a fresh equivalent.

**Critical privacy rule:** Never publish the value of `.AspNetCore.Components.TempData`. This applies to screenshots, HAR files, copied request headers, copied response headers, cookie tables, text exports, issue comments, and pull requests. Replace the complete value with `<redacted>` before saving or sharing evidence.

### Prepare two independent sessions

1. Close every Chrome Incognito window.
2. Close every Microsoft Edge InPrivate window.
3. Open Chrome.
4. Press **Ctrl+Shift+N** to create session A.
5. Confirm Chrome displays **Incognito**.
6. Open Microsoft Edge separately.
7. Press **Ctrl+Shift+N** to create session B.
8. Confirm Edge displays **InPrivate**.
9. Do not open a second Incognito window as session B. Windows in the same private mode can share one private cookie jar.

### Store and retain the value in session A

1. In the Chrome Incognito window, open `https://localhost:7171/`.
2. Press **F12** and prepare DevTools Network.
3. Enter the fresh value in **Message**.
4. Select **Peek** in **Redirect target**.
5. Click **Store and redirect**.
6. Confirm `/peek` loads.
7. Confirm **Peek result** exactly equals the fresh value.
8. Confirm `#peek-message` has marker `true`.
9. Record Request UTC.

**Evidence checkpoint - capture now:** Before leaving session A, include the Chrome **Incognito** indicator, `/peek` address, full Peek result, **Request UTC**, and selected `#peek-message` line with marker `true`. Do not include a cookie value. Save as `TC-18-01-session-A-peek.png`.

10. Leave session A on `/peek`. Do not reload it and do not click **Continue to ordinary reader** yet.

### Record session A cookie metadata without exposing its value

1. In session A DevTools, click the **Application** tab. If it is hidden, click the `>>` menu and then click **Application**.
2. In the left pane, expand **Storage** if necessary.
3. Expand **Cookies**.
4. Click `https://localhost:7171`.
5. Find the row whose name is `.AspNetCore.Components.TempData`.
6. Record only its name and non-secret attributes such as Domain, Path, Expires/Max-Age, Size, HttpOnly, Secure, and SameSite in `evidence\TC-18-cookie-metadata.txt`.
7. In that text file, write the value as `.AspNetCore.Components.TempData=<redacted>`.
8. Do not copy the real value to the evidence file.

**Evidence checkpoint - capture now:** Save `evidence\TC-18-cookie-metadata.txt` before switching to session B. Reopen the file and verify that it contains the cookie name, allowed attributes, and literal `<redacted>`, but no characters copied from the real Value cell. This text file is the cookie-metadata evidence; no raw cookie screenshot is required.

9. Do not take a screenshot of the raw cookie table. If organizational policy requires a cookie screenshot, cover the entire Value cell with an opaque rectangle before saving and inspect the final image at full size.

### Prove session B cannot read session A data

1. Switch to the Edge InPrivate window.
2. Enter `https://localhost:7171/read` directly in the address bar and press **Enter**.
3. Do not visit the seed page in session B.
4. Press **F12** and enable **Preserve log** on Network.
5. Press **Ctrl+R** once so the request is captured after DevTools opens.
6. Confirm the heading says **Ordinary consuming reader**.
7. Confirm both reads display `empty`.
8. Inspect `#first-read` and `#second-read` and confirm both markers are `false`.

**Evidence checkpoint - capture now:** Before leaving **Elements**, include the Edge **InPrivate** indicator, `/read` address, both `empty` results, **Request UTC**, and both output lines showing markers `false`. Save as `TC-18-02-session-B-empty.png`.

9. In Network, click the session B `read` row and confirm `GET /read` returned `200 OK`.

**Evidence checkpoint - capture now:** On session B **Network > Headers**, include the Edge **InPrivate** indicator, selected `read` row, and **General** showing `/read`, `GET`, and `200 OK`. Save as `TC-18-03-session-B-network.png`.

10. Leave session B open but do not submit any value there.

### Return to session A and consume its value

1. Switch back to the Chrome Incognito window.
2. Confirm it is still on `/peek` and still displays the session A value.
3. Click **Continue to ordinary reader**.
4. Confirm `/read` loads.
5. Confirm both reads exactly equal the session A value.
6. Confirm both output markers are `true`.

**Evidence checkpoint - capture now:** Before reloading session A, include the Chrome **Incognito** indicator, `/read` address, both complete session A values, **Request UTC**, and both output lines with markers `true`. Save as `TC-18-04-session-A-value.png`.

7. Click the page and press **Ctrl+R** once.
8. Confirm both reads are empty and both markers are `false`.
9. Confirm Request UTC changed.

**Evidence checkpoint - capture now:** Take two images before continuing. First, on **Elements**, include the Chrome **Incognito** indicator, both reloaded `empty` results, changed **Request UTC**, and both output lines with markers `false`; save as `TC-18-05-session-A-consumed.png`. Second, switch to **Network > Headers**, select the newest `read` row, and include `/read`, `GET`, and `200 OK`; save as `TC-18-06-session-A-consumed-network.png`.

10. Check the Console and server terminal for errors.
11. Before sharing evidence, search every `TC-18` file for `.AspNetCore.Components.TempData=` and confirm no real value follows the equals sign.

### Pass checklist

- Session A retains its Peek value while session B is tested.
- Session B receives no value and both markers are `false`.
- Returning to session A still produces its own value in both reads.
- Session A reload is empty after consumption.
- No artifact exposes a TempData cookie value.
- No browser or server error occurs.

## TC-19 - Repeated independent cycles

**Purpose:** Detect stale values, cross-run contamination, or errors that accumulate over repeated operations.

### Prepare one long-running session and result log

1. Start with a clean Chrome Incognito session once.
2. Open `https://localhost:7171/`.
3. Open DevTools.
4. On Network, check **Preserve log**.
5. On Console, open **Console settings** using its gear icon and check **Preserve log** if that option is available.
6. Keep the same Incognito window open for all nine cycles.
7. Create `evidence\TC-19-results.md`.
8. Add this table:

   | Cycle | Flow | Identifier | Initial result | Retained result | Ordinary read 1 | Ordinary read 2 | Final reload empty | Errors | Result |
   |---|---|---|---|---|---|---|---|---|---|
   | 1 | Ordinary | TC19-ORD-1 | | N/A | | | | | |
   | 2 | Ordinary | TC19-ORD-2 | | N/A | | | | | |
   | 3 | Ordinary | TC19-ORD-3 | | N/A | | | | | |
   | 4 | Peek | TC19-PEEK-1 | | | | | | | |
   | 5 | Peek | TC19-PEEK-2 | | | | | | | |
   | 6 | Peek | TC19-PEEK-3 | | | | | | | |
   | 7 | Keep | TC19-KEEP-1 | | N/A | | | | | |
   | 8 | Keep | TC19-KEEP-2 | | N/A | | | | | |
   | 9 | Keep | TC19-KEEP-3 | | N/A | | | | | |

9. Use the exact identifier shown in each row. Add a UTC suffix only if another tester could run against the same browser profile.

### Run ordinary cycles 1, 2, and 3

Run the following steps once with `TC19-ORD-1`, then again with `TC19-ORD-2`, and finally with `TC19-ORD-3`. Finish and record one cycle before starting the next.

1. If the browser is not on `/`, click **Seed another message**. For the first cycle, the browser is already on `/`.
2. Replace the generated **Message** with the current cycle identifier.
3. Select **Consume with ITempData**.
4. Click **Store and redirect**.
5. Confirm `/read` loads.
6. Confirm both reads exactly equal the current identifier.
7. Confirm neither read contains an identifier from an earlier cycle.
8. Confirm both markers are `true`.

**Evidence checkpoint - capture now:** Before reloading the current ordinary cycle, include the `/read` address, both complete current identifiers, **Request UTC**, and both output lines with markers `true`. Save as `TC-19-ORD-1-result.png`, replacing `1` with the current ordinary cycle number.

9. Press **Ctrl+R** once.
10. Confirm both reads display `empty` and both markers are `false`.

**Evidence checkpoint - capture now:** Before returning to the seed page, include both `empty` results, changed **Request UTC**, both output lines with markers `false`, and the newest successful `GET /read` row. Save as `TC-19-ORD-1-empty.png`, replacing `1` with the current ordinary cycle number.

11. In `evidence\TC-19-results.md`, enter `Pass` for both ordinary reads and final reload, or record the exact unexpected value.
12. Check Console for a new red error and record `None` or the complete error summary in the row.

### Run Peek cycles 4, 5, and 6

Run the following steps with `TC19-PEEK-1`, then `TC19-PEEK-2`, and finally `TC19-PEEK-3`.

1. From the final empty `/read` page, click **Seed another message**.
2. Replace **Message** with the current Peek identifier.
3. Select **Peek**.
4. Click **Store and redirect**.
5. Confirm `/peek` displays exactly the current identifier with marker `true`.
6. Record Request UTC.

**Evidence checkpoint - capture now:** Before reloading Peek, include the `/peek` address, complete current Peek identifier, first **Request UTC**, and `#peek-message` line with marker `true`. Save as `TC-19-PEEK-1-initial.png`, replacing `1` with the current Peek cycle number.

7. Click **Reload peek reader** once.
8. Confirm the same identifier remains, the marker remains `true`, and Request UTC changes.
9. Confirm no identifier from an earlier cycle appears.

**Evidence checkpoint - capture now:** Take two images before continuing to the ordinary reader. First, on **Elements**, include the retained identifier, changed **Request UTC**, and `#peek-message` line with marker `true`; save as `TC-19-PEEK-1-retained.png`, replacing `1` with the current Peek cycle number. Second, switch to **Network** and include both successful `peek` rows with the newest selected; save as `TC-19-PEEK-1-retained-network.png`, again replacing `1` with the current cycle number.

10. Click **Continue to ordinary reader**.
11. Confirm both reads exactly equal the current Peek identifier and both markers are `true`.

**Evidence checkpoint - capture now:** Before reloading `/read`, include both complete current Peek identifiers, **Request UTC**, and both output lines with markers `true`. Save as `TC-19-PEEK-1-read.png`, replacing `1` with the current Peek cycle number.

12. Press **Ctrl+R** once.
13. Confirm both reads are empty and both markers are `false`.

**Evidence checkpoint - capture now:** Before starting the next cycle, include both `empty` results, changed **Request UTC**, both output lines with markers `false`, and newest successful `GET /read` row. Save as `TC-19-PEEK-1-empty.png`, replacing `1` with the current Peek cycle number.

14. Complete the current Peek row in the result log before starting the next identifier.
15. Check Console and record any new error.

### Run Keep cycles 7, 8, and 9

Run the following steps with `TC19-KEEP-1`, then `TC19-KEEP-2`, and finally `TC19-KEEP-3`.

1. From the final empty `/read` page, click **Seed another message**.
2. Replace **Message** with the current Keep identifier.
3. Select **Read and keep once**.
4. Click **Store and redirect**.
5. Confirm `/keep-once` displays exactly the current identifier with marker `true`.
6. Confirm no identifier from an earlier cycle appears.

**Evidence checkpoint - capture now:** Before leaving `/keep-once`, include its address, complete current Keep identifier, **Request UTC**, full Get-then-Keep operation, and `#keep-message` line with marker `true`. Save as `TC-19-KEEP-1-initial.png`, replacing `1` with the current Keep cycle number.

7. Do not reload `/keep-once`.
8. Click **Continue to ordinary reader**.
9. Confirm both reads exactly equal the current Keep identifier and both markers are `true`.

**Evidence checkpoint - capture now:** Before reloading `/read`, include both complete current Keep identifiers, **Request UTC**, and both output lines with markers `true`. Save as `TC-19-KEEP-1-read.png`, replacing `1` with the current Keep cycle number.

10. Press **Ctrl+R** once.
11. Confirm both reads are empty and both markers are `false`.

**Evidence checkpoint - capture now:** Before starting the next cycle, include both `empty` results, changed **Request UTC**, both output lines with markers `false`, and newest successful `GET /read` row. Save as `TC-19-KEEP-1-empty.png`, replacing `1` with the current Keep cycle number.

12. Complete the current Keep row in the result log before starting the next identifier.
13. Check Console and record any new error.

### Perform final accumulated checks

1. Confirm the browser ends on an empty `/read` page after cycle 9.
2. Inspect both outputs and confirm both markers are `false`.

**Evidence checkpoint - capture now:** Before leaving the final page, include the `/read` address, both `empty` results, final **Request UTC**, and both output lines with markers `false`. Save as `TC-19-10-final-empty.png`.

3. Click **Network** and confirm the preserved list contains all nine cycle flows without a red request row.

**Evidence checkpoint - capture now:** Keep the Network list scrolled and sized to show as much of the nine-cycle request history as possible, including the final selected `GET /read` with `200 OK`. Take additional images with suffixes `-part-1`, `-part-2`, and so on if one screen cannot show the complete list. Save the first image as `TC-19-12-network.png`.

4. Open `evidence\TC-19-results.md` in VS Code.
5. Review all nine rows.
6. Confirm every row identifies only its own value.
7. Confirm every **Final reload empty** cell says `Pass`.

**Evidence checkpoint - capture now:** In VS Code, collapse side panels and zoom out only enough to keep every table value readable. Capture all nine completed rows and column headings. Use multiple images with `-part-1`, `-part-2`, and so on if needed. Save the first image as `TC-19-11-results.png`.

8. Return to Chrome, click **Console**, and confirm no unexplained red error accumulated.
9. Switch to the server PowerShell window.
10. Search visually for `fail:`, `Unhandled exception`, `Antiforgery`, or `Serialization` errors recorded during the nine cycles.

### Pass checklist

- All three ordinary cycles show only their own identifier and end empty.
- All three Peek cycles survive one Peek reload, reach the ordinary reader, and end empty.
- All three Keep cycles reach the ordinary reader without reloading the keep page and end empty.
- No value from an earlier cycle appears in a later cycle.
- No browser or server error accumulates.

## TC-20 - Keyboard and focus behavior

**Purpose:** Verify keyboard reachability, accessible names, destination focus, and responsive layout.

**Desktop test value:** `TC20-KEYBOARD-20260909-001` or a fresh equivalent.

**Mobile test value:** `TC20-MOBILE-LONG-MESSAGE-ABCDEFGHIJKLMNOPQRSTUVWXYZ-0123456789`.

### Set the desktop viewport to 1280 x 720

1. Start with a clean Chrome Incognito session.
2. Open `https://localhost:7171/`.
3. Press **F12**.
4. Press **Ctrl+Shift+M** to turn on the Device Toolbar.
5. In the device list above the page, select **Responsive**.
6. Click the width field and enter `1280`.
7. Click the height field and enter `720`.
8. Press **Enter**.
9. Confirm the size displayed in the toolbar is `1280 x 720`.

**Evidence checkpoint - capture now:** Before navigating or changing viewport size, include the Device Toolbar showing **Responsive** and `1280 x 720`, the complete browser viewport, and the **Seed a TempData message** heading. Save as `TC-20-01-desktop-1280x720.png`.

### Test the complete keyboard flow

1. Press **Ctrl+L**.
2. Type `https://localhost:7171/` and press **Enter**.
3. Wait until **Seed a TempData message** appears.
4. Do not use the mouse inside the page for the remainder of this subsection.
5. Press **Tab** and **Shift+Tab** as needed to move among links and form controls.
6. Confirm each focused item has a visible outline or other visible focus indicator.
7. Confirm focus is not trapped; repeated **Tab** moves forward and **Shift+Tab** moves backward.
8. When focus reaches **Message**, press **Ctrl+A** and type the desktop test value.
9. Press **Tab** to move to **Redirect target**.
10. Press **Home** to select the first option, **Consume with ITempData**. If the list opens instead, use **Up Arrow** until that option is selected and press **Enter**.
11. Press **Tab** to move to **Store and redirect**.
12. Press **Enter**.
13. Wait for `/read` to load.
14. Confirm both reads exactly equal the desktop test value.
15. Press **F12** if DevTools is closed.
16. Click the **Console** tab.
17. Type the following expression and press **Enter**:

   ```javascript
   `${document.activeElement?.tagName}:${document.activeElement?.textContent?.trim()}`
   ```

18. Confirm the result starts with `H1:` and contains `Ordinary consuming reader`.

**Evidence checkpoint - capture now:** Before closing DevTools, include the `/read` address, **Ordinary consuming reader** heading, both returned values, and **Console** with the complete `document.activeElement` expression and `H1:Ordinary consuming reader` result. Save as `TC-20-02-destination-focus.png`.

19. Press **F12** to close DevTools and return keyboard focus to the page.
20. Press **Tab** until **Reload consuming reader** receives focus.
21. Confirm its focus indicator is visible.
22. Press **Tab** until **Seed another message** receives focus.
23. Confirm its focus indicator is visible.

**Evidence checkpoint - capture now:** While **Seed another message** still has keyboard focus, capture the `/read` page area containing both result-action links. Ensure the focused link's visible outline and the neighboring **Reload consuming reader** link are both visible. Save as `TC-20-03-result-actions-focus.png`.

### Inspect accessible names and roles

1. Press **F12** to reopen DevTools.
2. Return to `https://localhost:7171/` by entering it in the address bar.
3. Click the **Elements** tab.
4. Click the element-picker icon at the upper-left of DevTools.
5. Click the **Message** textbox on the page.
6. In the right-side Elements pane, click **Accessibility**. If hidden, click `>>` and select **Accessibility**.
7. Expand **Computed Properties**.
8. Confirm the input's role is `textbox` and its computed name is `Message`.

**Evidence checkpoint - capture now:** Keep the Message `<input>` selected in **Elements**. Include the visible **Message** label/input and **Accessibility > Computed Properties** showing role `textbox` and name `Message`. Save as `TC-20-04-message-accessibility.png`.

9. Click the element-picker icon again and click **Redirect target**.
10. Confirm its role is `combobox` and computed name is `Redirect target`.

**Evidence checkpoint - capture now:** Keep the target `<select>` selected. Include the visible **Redirect target** label/list and Accessibility pane showing role `combobox` and name `Redirect target`. Save as `TC-20-05-target-accessibility.png`.

11. Click the element-picker icon again and click **Store and redirect**.
12. Confirm its role is `button` and computed name is `Store and redirect`.

**Evidence checkpoint - capture now:** Keep the submit `<button>` selected. Include the visible **Store and redirect** button and Accessibility pane showing role `button` and name `Store and redirect`. Save as `TC-20-06-submit-accessibility.png`.

13. Navigate to `/read`.
14. Select the **Ordinary consuming reader** heading with the element picker.
15. Confirm its role is `heading` and level is `1`.
16. Select `#first-read` with the element picker.
17. Confirm the browser exposes it as output/status content and exposes its text value.

**Evidence checkpoint - capture now:** Keep `#first-read` selected. Include the **Ordinary consuming reader** heading, visible **First read** result, selected output element, and Accessibility pane showing the result's role/content. Save as `TC-20-07-result-accessibility.png`. The heading level is verified in step 18; the result role is what this image captures.

### Check desktop overflow

1. Click the **Console** tab.
2. Enter the following expression and press **Enter**:

   ```javascript
   document.documentElement.scrollWidth <= document.documentElement.clientWidth
   ```

3. Confirm the result is `true` at `1280 x 720`.

**Evidence checkpoint - capture now:** Keep the Device Toolbar size and **Console** visible together. Include `1280 x 720`, the complete overflow expression, and returned `true`. Save as `TC-20-08-desktop-overflow.png`.

### Set and test the mobile viewport at 390 x 844

1. Press **Ctrl+Shift+M** if the Device Toolbar is not visible.
2. Select **Responsive**.
3. Enter `390` in the width field.
4. Enter `844` in the height field.
5. Press **Enter**.
6. Navigate to `https://localhost:7171/`.
7. Confirm the size displayed in the Device Toolbar is `390 x 844`.
8. Confirm the navigation links are collapsed and the menu control is visible near the top-right of the green header.
9. Press **Ctrl+L**, type `https://localhost:7171/`, and press **Enter** to place navigation focus on the page heading.
10. Press **Shift+Tab** to move backward through **TempData docs** and **Issue #69134** until the **Navigation menu** control receives focus.
11. Confirm the menu control has a visible focus indicator.
12. Press **Space** once.
13. Confirm the navigation list opens and shows **Seed message**, **Consume**, **Bound clear**, and the remaining routes.
14. Confirm the open menu does not cover or overlap the page heading or form.
15. Press **Tab** and confirm keyboard focus moves into the displayed navigation links.

**Evidence checkpoint - capture now:** Before closing the menu, capture the Device Toolbar showing `390 x 844`, the complete open navigation list, the focused navigation link and its visible focus indicator, and the beginning of the heading/form showing that no overlap occurs. Save as `TC-20-09-mobile-menu.png`.

16. Press **Shift+Tab** to return to **Navigation menu**.
17. Press **Space** and confirm the navigation list closes.

### Check long mobile content and overflow

1. With the menu closed, press **Tab** until **Message** receives focus, or click **Message** for this layout-only subsection.
2. Press **Ctrl+A** and enter the mobile test value.
3. Select **Consume with ITempData**.
4. Click **Store and redirect**.
5. Confirm `/read` displays the full mobile test value in both rows.
6. Confirm neither value overlaps its label, buttons, or page edge.
7. Confirm **Reload consuming reader** and **Seed another message** remain inside the viewport.

**Evidence checkpoint - capture now:** Before opening Console, include the Device Toolbar showing `390 x 844`, `/read` address, both complete long values, labels, **Request UTC**, and both action links. Capture enough vertical area, using `TC-20-10-mobile-long-content-part-2.png` if scrolling is required, so every non-overlap claim is visible. Save the first image as `TC-20-10-mobile-long-content.png`.

8. Open the **Console** tab.
9. Enter the following expression and press **Enter**:

   ```javascript
   document.documentElement.scrollWidth <= document.documentElement.clientWidth
   ```

10. Confirm the result is `true` at `390 x 844`.

**Evidence checkpoint - capture now:** Keep the Device Toolbar size and **Console** visible together. Include `390 x 844`, the complete overflow expression, and returned `true`. Save as `TC-20-11-mobile-overflow.png`.

11. Check Console and the server terminal for errors.
12. Press **Ctrl+Shift+M** when finished to turn off device emulation.

### Pass checklist

- All tested links and controls are reachable in both Tab directions with a visible focus indicator.
- Submitting by keyboard moves document focus to the destination `h1`.
- The textbox, select, button, heading, and result expose the expected names, roles, and content.
- The mobile navigation opens, closes, and exposes its links by keyboard.
- Desktop and mobile overflow checks both return `true`.
- No text overlaps or escapes the viewport and no browser or server error occurs.

## TC-21 - Error and diagnostic sweep

**Purpose:** Prove that successful UI behavior is not accompanied by hidden browser, network, server, route, or build failures.

**Privacy rule:** Export only a sanitized HAR. Never publish Cookie, Set-Cookie, Authorization, or `.AspNetCore.Components.TempData` values from a HAR, screenshot, Console export, header copy, or text file.

### Prepare diagnostic capture

1. Start with a clean Chrome Incognito session.
2. Open `https://localhost:7171/`.
3. Press **F12**.
4. Click **Network**.
5. Check **Preserve log**.
6. Click the **Clear network log** icon.
7. Click **Console**.
8. Click the **Console settings** gear icon.
9. Check **Preserve log** if the option is available.
10. Close the settings popover.
11. Click the **Clear console** icon.
12. Confirm the server PowerShell window from **Start the sample and save its log** is still open.

### Review errors while running core tests

Perform these checks after every testcase when running `TC-01` through `TC-20`:

1. Click **Console**.
2. Look for red rows, `Unhandled`, `Exception`, `fail`, rejected promises, Blazor errors, or resource-load failures.
3. If an unexpected row appears, do not clear it.
4. Expand the row using its disclosure arrow.

**Evidence checkpoint - capture immediately if an error appears:** Before changing tabs or continuing the testcase, include the page address/heading and **Console** with the full expanded message, source link, and stack trace. Save as `TC-21-error-<testcase>-console-<UTC timestamp>.png`, replacing both placeholders, for example `TC-21-error-TC-05-console-20260909-143000Z.png`.

5. Write down the testcase and exact action immediately before the error.
6. Click **Network**.
7. Look for red request rows or a status beginning with `4` or `5`.
8. Click every such row and record its URL, method, status, response body, and relevant headers.

**Evidence checkpoint - capture immediately if a request failed:** Keep the failed row selected. Capture the page address, failed Network row, **Headers > General** with URL/method/status, and **Response** tab content in a second image if it does not fit. Name the first image `TC-21-error-<testcase>-network-<UTC timestamp>.png` and add `-response` before `.png` to the second. Redact cookie and authorization values.

9. Treat an expected `302` redirect in the seed flow as success, not failure.
10. Treat a normal `304 Not Modified` cache-validation response as non-failing unless content is wrong.
11. Check the server PowerShell window for `fail:`, `Unhandled exception`, antiforgery errors, serialization errors, or routing errors.

**Evidence checkpoint - capture immediately if a server failure appears:** Before running another request, capture the PowerShell window with the complete `fail:`/exception entry, stack trace, preceding request log line, and prompt area. Save as `TC-21-error-<testcase>-server-<UTC timestamp>.png`. Also preserve the same text in `evidence\manual-server.log`.

12. Redact any cookie or authorization value in the captured material.

### Run the final route sweep

1. Keep **Preserve log** enabled in Network.
2. Create `evidence\TC-21-route-sweep.md`.
3. Add this table:

   | Route | Expected heading | Method | Status | Console error | Server error | Evidence file | Result |
   |---|---|---|---|---|---|---|---|
   | `/` | Seed a TempData message | GET | | | | `TC-21-route-01-root.png` | |
   | `/read` | Ordinary consuming reader | GET | | | | `TC-21-route-02-read.png` | |
   | `/read-bound-clear` | Bound reader that clears the property | GET | | | | `TC-21-route-03-bound-clear.png` | |
   | `/read-bound-untouched` | Bound reader that leaves the property untouched | GET | | | | `TC-21-route-04-bound-untouched.png` | |
   | `/peek` | Non-consuming reader | GET | | | | `TC-21-route-05-peek.png` | |
   | `/keep-once` | Read and keep once | GET | | | | `TC-21-route-06-keep-once.png` | |
   | `/untouched` | TempData untouched | GET | | | | `TC-21-route-07-untouched.png` | |
   | `/peek-keep` | Peek, then keep the same key | GET | | | | `TC-21-route-08-peek-keep.png` | |
   | `/same-request` | Write and read in the same request | GET | | | | `TC-21-route-09-same-request.png` | |

4. For the first route, click the address bar, type `https://localhost:7171/`, and press **Enter**.
5. Confirm the visible `h1` exactly matches the table.
6. In Network, click that document request.
7. Under **Headers > General**, confirm the method is `GET` and status is `200 OK`.
8. Check Console and the server terminal for a new error.

**Evidence checkpoint - capture now for this route:** Before entering the next URL, return to **Network > Headers** and keep this route's document row selected. Include the full browser address, exact visible `h1`, selected request row, and **General** showing URL, `GET`, and `200 OK`. Save with the **Evidence file** name in that route's table row. Repeat this checkpoint for all nine routes.

9. Fill in that route's row and set **Result** to `Pass` only if the heading, status, Console, server, and route-specific evidence checks all pass.
10. Repeat steps 4 through 9 for each remaining route by appending its path to `https://localhost:7171`.
11. Do not submit any forms during this sweep.
12. Confirm the completed Network list contains nine successful document requests.

**Evidence checkpoint - capture now:** Before leaving Network, size or scroll the request list to show all nine document rows and their `200` statuses. Include the final selected request's **General** section. Use `-part-2` if all rows cannot fit. Save the first image as `TC-21-01-route-network.png`.

13. Open `evidence\TC-21-route-sweep.md` in VS Code and confirm all nine rows are complete.

**Evidence checkpoint - capture now:** In VS Code, include the table header and all nine completed rows, with Method, Status, error columns, evidence filename, and Result readable. Use `-part-2` if needed. Save the first image as `TC-21-02-route-results.png`.

### Check the hidden Blazor error UI

1. On the final route, click **Console**.
2. Enter the following expression and press **Enter**:

   ```javascript
   getComputedStyle(document.querySelector('#blazor-error-ui')).display
   ```

3. Confirm the result is `none`.

**Evidence checkpoint - capture now:** Before leaving Console, include the final route's address/heading and **Console** with the complete `getComputedStyle(...)` expression and returned `none`. Save as `TC-21-03-error-ui-hidden.png`.

### Export sanitized browser evidence

1. Click **Network**.
2. Click the export/download arrow in the Network toolbar.
3. Choose **Export HAR (sanitized)** if Chrome displays a choice.
4. In the Save dialog, browse to `D:\TempDataValidation\evidence` and enter `TC-21-network-sanitized.har` in **File name**.

**Evidence checkpoint - capture now:** Confirm the selected export is the sanitized variant, then click **Save** in the dialog. The file created at `D:\TempDataValidation\evidence\TC-21-network-sanitized.har` is the evidence for this exact Network state.

5. If Chrome offers only an export that includes sensitive data, click **Cancel**, do not create a HAR, and skip to step 10.

**Evidence checkpoint - fallback only:** When the sanitized export option is unavailable, confirm the Save dialog is closed and no HAR was created. Use the already captured `TC-21-01-route-network.png` as the Network evidence; do not take a screenshot of an unsanitized export dialog or file.

6. Open the sanitized HAR in VS Code.
7. Press **Ctrl+F** and search for `.AspNetCore.Components.TempData`.
8. If the cookie name exists, confirm no usable value appears beside it. Replace any value with `<redacted>` before the file is committed or shared.
9. Search the HAR for `Cookie`, `Set-Cookie`, and `Authorization` and confirm their values are absent or redacted.

**Evidence checkpoint - capture now:** Save and close `evidence\TC-21-network-sanitized.har` only after all four searches are clean: `.AspNetCore.Components.TempData`, `Cookie`, `Set-Cookie`, and `Authorization`. The sanitized HAR file itself is the evidence; do not create a screenshot that could expose a raw value.

10. Return to Chrome and click **Console**.
11. Right-click an empty area in Console.
12. Click **Save as** if available and save `D:\TempDataValidation\evidence\TC-21-console.txt`.
13. If **Save as** is unavailable, select the visible Console rows, copy them, and paste them into `evidence\TC-21-console.txt`.
14. If Console has no errors, write `No browser Console errors observed` in that file.
15. Inspect the Console text file for cookie or authorization values before sharing it.

**Evidence checkpoint - capture now:** Reopen `evidence\TC-21-console.txt`, confirm it contains either the complete classified messages or the explicit no-errors statement, and verify no cookie/authorization value is present. Save the file. This text file is the Console evidence.

### Stop the sample and run the final build

1. Switch to the PowerShell window running the sample.
2. Press **Ctrl+C** once.
3. Wait until the command prompt returns. This releases build output files.
4. Confirm the prompt is at `D:\TempDataValidation`. If not, type `Set-Location D:\TempDataValidation` and press **Enter**.
5. Type the following command and press **Enter**:

   ```powershell
   dotnet --version | Tee-Object -FilePath .\evidence\TC-21-final-build.txt
   ```

6. Confirm the displayed SDK version is `11.0.100-rc.1.26425.128`.

**Evidence checkpoint - captured automatically:** At this exact point, `evidence\TC-21-final-build.txt` contains the SDK version. Do not run the build until the displayed version has been confirmed.

7. Type the following command and press **Enter**:

   ```powershell
   dotnet build .\TempDataValidation.sln --no-restore 2>&1 |
       Tee-Object -FilePath .\evidence\TC-21-final-build.txt -Append
   ```

8. Wait for the build command to finish and the prompt to return.
9. Confirm the final summary reports `0 Warning(s)`.
10. Confirm the final summary reports `0 Error(s)`.
11. If `NETSDK1057` appears, record it as an informational prerelease-SDK notice; do not count it as a product warning when the build summary remains zero warnings.

**Evidence checkpoint - capture now:** Before closing PowerShell, include the build command and final lines showing `Build succeeded`, `0 Warning(s)`, `0 Error(s)`, and the returned prompt. Save as `TC-21-04-final-build.png`. The complete text is already captured in `evidence\TC-21-final-build.txt` by `Tee-Object`.

12. Open `evidence\TC-21-final-build.txt` and confirm it contains both the SDK version and complete build summary.
13. Confirm `evidence\manual-server.log` exists and contains the server output from the manual run.
14. Review the server log one final time for `fail:` or `Unhandled exception`.

**Evidence checkpoint - capture now:** In VS Code, search `evidence\manual-server.log` for `fail:` and then `Unhandled exception`. With the search result count and relevant final log lines visible, save `TC-21-05-server-log-review.png`. Keep `evidence\manual-server.log` as the full-text server evidence.

### Pass checklist

- All nine routes return `GET` status `200 OK` and render the exact expected heading.
- Console contains no unexplained error, the Blazor error UI is hidden, and the server log contains no application exception.
- Any HAR or text export is sanitized and contains no secret cookie or authorization value.
- The pinned SDK version is `11.0.100-rc.1.26425.128`.
- The final build reports `0 Warning(s)` and `0 Error(s)`.

## Result recording template

Use one row per testcase in your execution record:

| Testcase | Result | Message identifier(s) | UTC executed | Evidence paths | Notes or issue classification |
|---|---|---|---|---|---|
| TC-01 | Not run | N/A | | | |
| TC-02 | Not run | | | | |
| TC-03 | Not run | | | | |
| TC-04 | Not run | | | | |
| TC-05 | Not run | | | | |
| TC-06 | Not run | | | | |
| TC-07 | Not run | | | | |
| TC-08 | Not run | | | | |
| TC-09 | Not run | | | | |
| TC-10 | Not run | | | | |
| TC-11 | Not run | | | | |
| TC-12 | Not run | | | | |
| TC-13 | Not run | | | | |
| TC-14 | Not run | | | | |
| TC-15 | Not run | | | | |
| TC-16 | Not run | | | | |
| TC-17 | Not run | | | | |
| TC-18 | Not run | | | | |
| TC-19 | Not run | | | | |
| TC-20 | Not run | | | | |
| TC-21 | Not run | N/A | | | |
