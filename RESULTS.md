# UI lab results

Tool parts that I chose: Part 1 (Selenium IDE) and Part 4 (Cypress)

## Part 0
- Step 0.1, Chrome version: `158.0.8127.37`
- Step 0.3, ChromeDriver version: `ChromeDriver 158.0.8127.37 (b8c2be851f62f6a394ec57955d7f31335d7cb6d4-refs/branch-heads/8127@{#1692})`
- Step 0.4, locator of Pizza, Please!: `button[name="pizza-please"]` (CSS) / `name=pizza-please`
- Step 0.4, locator of Excluded tools: `select` (CSS)
- Step 0.4, locator of Must be vegetarian: `#default-checkbox` (CSS) / `id=default-checkbox`
- Step 0.4, locator of Custom Pizza Name: `input#pizza-name` (CSS)
- Step 0.5, pizza name, tool and calories of the run by hand: `Lab Pizza 20261125-003107`, Tool: `Pizza cutter`, Calories per slice: `325`

| Step | Actual result | Pass or fail |
|------|---------------|--------------|
| 1 | The page opened and the `h1` heading showed `Looking to break out of your pizza routine?`. | Pass |
| 2 | Turning on `Advanced` revealed `Max Calories per Slice` (default `1000`), `Excluded tools`, `Must be vegetarian`, and `Custom Pizza Name`. | Pass |
| 3 | Replaced `1000` with `600`, selected `Knife` in `Excluded tools`, ticked `Must be vegetarian`, and entered `Lab Pizza 20261125-003107`; all four fields displayed these values. | Pass |
| 4 | Clicked `Pizza, Please!`; recommendation appeared showing `Name: Lab Pizza 20261125-003107`, `Tool: Pizza cutter` (not `Knife`), and `Calories per slice: 325` (<= 600). | Pass |
| 5 | Clicked `Love it!` without logging in; the page displayed `Please log in first.` below the rating buttons. | Pass |
| 6 | Opened `/admin`, verified `admin` / `admin` in both fields, and clicked `Sign in`; the page displayed `Latest pizza recommendations`. | Pass |
| 7 | Found `Lab Pizza 20261125-003107 (tool=Pizza cutter, ingredients_number=5)` in the list, matching the tool `Pizza cutter` from step 4. | Pass |

## Part 1: Selenium IDE
- Firefox version and Node.js version: Node.js `v26.13.1` (npm `11.20.0`); playback executed in Chrome `158.0.8127.37` via `selenium-side-runner@4.0.13`
- Step 1.4, the line of the failed check and the line Tests:
  - Failed check: `Actual value 'Received!' did not match 'Received' during assertText:id=message:Received`
  - Line `Tests:`: `Tests: 1 failed, 1 total` (restored `!` and re-ran to `Tests: 1 passed, 1 total`)
- Step 1.5, target chosen for Submit, and why the recorded one was fragile:
  - Chosen target: `xpath=//button[@type='submit']`
  - Why fragile: The recorded target `css=.btn` relies on a visual styling class (`btn`) that multiple buttons can share; adding another styled button above Submit would cause the test to click the wrong button.
- Step 1.6, commands changed, added or deleted after recording:
  - Changed `open` to use full URLs (`https://www.selenium.dev/selenium/web/web-form.html` and `https://quickpizza.grafana.com/`), replaced fragile targets with stable label-based XPath (`xpath=//label[.//span[normalize-space()='Advanced']]`, `xpath=//label[normalize-space()='Max Calories per Slice']/../input`) and `css=input#pizza-name`.
  - Added explicit waits (`waitForElementVisible` for `css=h1`, the four advanced fields, `xpath=//p[starts-with(., 'Name:')]`, and `id=rate-result`; `waitForElementPresent` for `xpath=//select/option[normalize-space()='Knife']`), `executeScript` + `echo` to generate a unique timestamped `pizzaName`, and assertions for every expected result (`assertText`, `assertValue`, `assertSelectedLabel`, `assertChecked`, `assertNotText`, `storeText` + `executeScript` + `assert` for calories <= 600).
  - Removed redundant click/focus commands before typing and replaced hardcoded pizza name with `${pizzaName}`.
- Step 1.6, the pizza names of run 1 and run 2, from the lines `info: echo:`:
  - Run 1: `Lab Pizza 20261125-002929` (web form 1726 ms, QP-01 customer 4419 ms, total 6.64 s)
  - Run 2: `Lab Pizza 20261125-003044` (web form 797 ms, QP-01 customer 4907 ms, total 6.187 s)
- Step 1.6, the line `Tests:` of both runs:
  - Run 1: `Tests: 2 passed, 2 total`
  - Run 2: `Tests: 2 passed, 2 total`
- Step 1.7, one line of `QP01customerTest.java` that needs rewriting, and why:
  - `driver.findElement(By.xpath("//label[normalize-space()='Max Calories per Slice']/../input")).sendKeys("600");` — it does not call `.clear()` before `sendKeys("600")`, so because QuickPizza pre-fills the box with `1000`, the input would end up with `1000600` instead of `600`. (In addition, `assertThat(value, is("${pizzaName}"));` compares against the literal string `"${pizzaName}"` instead of `vars.get("pizzaName").toString()`.)

## Part 4: Cypress
- `node --version`, `npm --version` and `npx cypress version`:
  - `node --version`: `v26.13.1`
  - `npm --version`: `11.20.0`
  - `npx cypress version`: Cypress package version `16.3.0`, Cypress binary version `16.3.0`, Electron `43.8.4`, Bundled Node `24.22.1` (upgraded from `16.2.1` to `16.3.0` to resolve a Chrome 158 multi-spec `* is not a valid URL` CDP error)
- Step 4.3, failure message for the wrong title:
  - `Timed out retrying after 10000ms: expected 'Web form' to equal 'Web Form'` (`-'Web form'` / `+'Web Form'`)
- Step 4.6, tool and calories in the answer of `POST /api/pizza`:
  - Run 1: `Tool: Pizza cutter`, `Calories per slice: 475`
  - Run 2: `Tool: Pizza cutter`, `Calories per slice: 425`
- Step 4.7, the line of the admin list that shows your pizza:
  - Run 1: `Lab Pizza 20261125-003323 (tool=Pizza cutter, ingredients_number=7)`
  - Run 2: `Lab Pizza 20261125-003402 (tool=Pizza cutter, ingredients_number=6) (newest)`
- Step 4.8, run 1, result, duration and pizza name:
  - Result: `All specs passed!` (2 tests, 2 passing, 0 failing)
  - Duration: `00:12` (`pizza-scenario.cy.js` `00:10`, `web-form.cy.js` `00:02`)
  - Pizza name: `Lab Pizza 20261125-003323`
- Step 4.8, run 2, result, duration and pizza name:
  - Result: `All specs passed!` (2 tests, 2 passing, 0 failing)
  - Duration: `00:10` (`pizza-scenario.cy.js` `00:08`, `web-form.cy.js` `00:01`)
  - Pizza name: `Lab Pizza 20261125-003402`
- Step 4.9 (optional), browser and result: Not run (only Google Chrome `158.0.8127.37` is installed on this machine, as confirmed by `npx cypress info`).

## Part B
1. On QuickPizza, all three number inputs (`Max Calories per Slice`, `Min Number of Toppings`, and `Max Number of Toppings`) share the duplicate id `floating_filled`, so `$$("#floating_filled").length` returns `3` and `#floating_filled` cannot uniquely identify the calories field. Therefore, both my Selenium IDE test (`xpath=//label[normalize-space()='Max Calories per Slice']/../input`) and Cypress test (`cy.contains('label', 'Max Calories per Slice').siblings('input')`) locate the box through its visible label text instead. To let tests use an id safely, QuickPizza should give each input a unique semantic `id` (such as `id="max-calories-per-slice"`) paired with a matching `for` attribute on its `<label>`. That change helps a tester because `id` selectors are shorter, faster, and immune to wording or layout changes around the label.
2. Comparing the tool shown in the `/admin` list (`Lab Pizza 20261125-003402 (tool=Pizza cutter, ingredients_number=6)`) against the tool returned in step 4 (`Tool: Pizza cutter`) is a **consistency oracle** (cross-checking two views of the same entity within the system). It does not need to predict which random tool the server will pick, only that the server persists and displays the same tool consistently across the customer and admin views. A concrete fault this check would catch is a persistence or mapping bug where the backend returns one tool in `POST /api/pizza` (e.g., `Pizza cutter`) but saves a default or wrong tool (e.g., `Knife` or an empty string) to the database read by `/admin`.
3. Currently QP-01 ticks `Must be vegetarian` in step 3, but step 4 only asserts the pizza name, excluded tool, and calories. To verify the vegetarian constraint, the test can intercept `POST /api/pizza` (as in Step 4.6) and assert both `expect(request.body.mustBeVegetarian).to.equal(true)` and `expect(response.body.vegetarian).to.equal(true)`, and additionally cross-check every ingredient in `response.body.pizza.ingredients` against a known list of non-vegetarian toppings (or a `vegetarian: true` flag on each ingredient). From the page alone, the UI does not display a vegetarian badge or ingredient dietary metadata, so checking the API response payload (or adding a vegetarian indicator/flag to the UI or API ingredient objects) is required to make the check reliable.
4. Selenium IDE required an explicit `waitForElementVisible` or `waitForElementPresent` command before every asynchronous element on QuickPizza (the `h1` heading, the four Advanced inputs, the `Knife` `<option>`, the recommendation `<p>` lines, and `#rate-result`), whereas Cypress automatically retried every `cy.get`, `cy.contains`, and `.should` up to `defaultCommandTimeout: 10000` (only needing `cy.get('#svelte-announcer').should('exist')` before submitting the SvelteKit admin login form). Writing QP-01 was much faster and cleaner in Cypress because JavaScript variables, `cy.intercept`, numeric comparisons (`expect(calories).to.be.at.most(600)`), and the reusable `cy.adminLogin` custom command fit naturally in one readable file, whereas Selenium IDE needed 29 rigid rows including awkward `executeScript` workarounds for timestamps and calorie checks and did not cover the admin flow in Part 1. For evidence and debugging, Cypress was far more readable: `selenium-side-runner` printed plain Jest terminal lines (taking ~6.2–6.6 s) with no screenshots or video, while Cypress produced full-page screenshots (`qp01-step4-recommendation.png`, `qp01-step5-login-needed.png`, `qp01-step7-admin-list.png`) and MP4 videos (`pizza-scenario.cy.js.mp4`, `web-form.cy.js.mp4`) showing the Command Log side-by-side with the page in ~10–12 s.
5. I would choose **Cypress** for the automated UI tests of Project 2. First, Cypress eliminates most flaky timing bugs out of the box through built-in command retryability and `cy.intercept`, which let us verify both the UI and the underlying `POST /api/pizza` payload/response cleanly in one test. Second, Cypress supports maintainable custom commands (`cy.adminLogin`), easy environment-variable password handling, and automatic video and screenshot capture on every headless run, which directly satisfies Project 2's requirements for reusable code and visual evidence.
6. In Part 1 (Step 1.5), the recorder chose `css=.btn` for the Submit button, which is fragile because `.btn` is a generic Bootstrap styling class that many buttons on a page can share; if another button with class `btn` were added above Submit, `css=.btn` would match and click the wrong element. Replacing it with `xpath=//button[@type='submit']` is much more stable because `type="submit"` describes the semantic role of the form's submit button rather than its visual appearance, so styling or layout changes will not break the locator.
7. In Part 4 (Cypress), QP-01 must run as a single `it(...)` test because Cypress automatically clears cookies and browser storage before each new test starts. QuickPizza runs behind multiple backend instances and uses a session cookie to keep a browser pinned to the same server instance whose in-memory list `/admin` displays. If steps 6 and 7 were placed in a second `it(...)` test, Cypress would wipe that cookie between the two tests, so `/admin` could be routed to a different backend server that does not have the pizza created in steps 1–5, causing step 7 to fail intermittently even when the application works properly.
