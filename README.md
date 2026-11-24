# UI Lab — Automated UI Testing

This repository contains my work for the ITDS362 Automated UI Testing lab using **Part 1: Selenium IDE** and **Part 4: Cypress**.

## Tool Parts

### `selenium-ide/` (Part 1: Selenium IDE)
Holds the Selenium IDE project (`ui-lab.side`) containing the `web form` and `QP-01 customer` automated tests with stable locators and explicit waits, along with the exported JUnit Java file (`QP01customerTest.java`).

Run the tests in Chrome (after putting the `CHROMEDRIVER` folder on `PATH`):
```powershell
cd selenium-ide
$env:Path = (Split-Path $env:CHROMEDRIVER) + ";" + $env:Path
npx --yes selenium-side-runner@4.0.13 ui-lab.side
```

### `cypress/` (Part 4: Cypress)
Holds the Cypress end-to-end test project (`cypress.config.js`, `cypress/e2e/web-form.cy.js`, `cypress/e2e/pizza-scenario.cy.js`, and the custom `cy.adminLogin` command in `cypress/support/commands.js`), together with the video recordings (`cypress/videos/`) and screenshots (`cypress/screenshots/`) from full passing runs.

Install dependencies and run all specs in Chrome:
```powershell
cd cypress
npm install
npx cypress run --browser chrome
```
