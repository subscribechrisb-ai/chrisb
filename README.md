# chrisb
just my projects
[README.md](https://github.com/user-attachments/files/34136812/README.md)
# Rental Property Investment Calculator

A free, single-page calculator for Indianapolis-area rental property investors, branded for **Intrigue Real Estate & Property Management**. Enter a deal's numbers and it shows cash flow, cap rate, cash-on-cash return, equity, and a full 30-year projection. Everything updates as you type.

## How to run it

There is no build step, no server and no dependencies to install. The whole app is one file, `index.html`.

**On your computer:** download `index.html` and double-click it to open it in any modern browser.

**On the web with GitHub Pages:**
1. Push `index.html` to the root of this repository.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, set the source to **Deploy from a branch**, pick `main` and `/ (root)`, and save.
4. After a minute or two, the site is live at `https://<your-username>.github.io/<repo-name>/`.

The page needs an internet connection for two things it loads from public CDNs:
- **Chart.js 4.4.1** (cdnjs) draws the 30-year chart. If it fails to load, every number and the table still work; only the chart is skipped.
- **Raleway** (Google Fonts) is the typeface. If it fails, the page falls back to the system font.

## What it does

- **Deal inputs.** Ten core inputs: price, value after renovation, renovation cost, down payment, interest rate, loan term, rent, taxes, insurance and HOA. They come pre-filled with an example Indianapolis-area duplex.
- **Calculated values.** Loan amount, monthly principal and interest, closing costs and total cash required.
- **Advanced assumptions** (collapsed by default). Vacancy, maintenance and management percentages; seven utility lines; ten miscellaneous expense lines; growth rates; building-to-land ratio; appreciation; and closing costs to buy and to sell.
- **Headline cards.** Year-1 cap rate, cash-on-cash return, Year-1 monthly cash flow, total cash required and Year-5 equity.
- **Chart.** Annual cash flow, cumulative cash flow and equity over 30 years.
- **30-year table.** Cash flow, tax items, equity and returns (cap rate, cash-on-cash, ROE, annualized return, IRR if sold, ROI) for every year. The rent, tax, insurance, HOA, utility, miscellaneous, maintenance and management rows are editable per year, and edited cells are highlighted. A **Reset table edits** button clears them.
- **Contact form.** Fields for name, email, phone, property address and message, with validation.

## Design decisions

- **One self-contained file.** HTML, CSS and JavaScript live in `index.html`, and the logo is embedded as a data URI. That makes it trivial to host, email or drop into an existing site.
- **Plain JavaScript, no framework.** The app is small enough that a framework would add weight without adding much.
- **Live recalculation.** Results update on every keystroke instead of after a "Calculate" button, so investors can see how each input moves the outcome.
- **Simple first, detail on demand.** Only ten inputs are visible up front. Everything else sits in a collapsible "Advanced assumptions" section with sensible defaults.
- **Forgiving input handling.** Instead of blocking the user with errors, the calculator corrects bad values and says what it did under the field:
  - Blank or negative entries count as 0.
  - Down payment is capped at 100%.
  - Loan term is rounded and kept between 1 and 50 years.
- **Year-by-year overrides.** Real deals rarely grow in a straight line (a tax reassessment, a rent bump after renovation), so the table lets users replace a projected amount for any year. Later years keep growing from the edited amount. Maintenance and management stay a percent of rent unless edited directly.
- **Accessibility.**
  - Every input has a visible label and helper text linked with `aria-describedby`.
  - Results announce through `aria-live` regions.
  - Focus outlines are visible, and touch targets are at least 44px.
  - The chart has a text label.
  - The table has sticky row and column headers and is keyboard-scrollable.
- **Responsive, with dark mode.** Layout works from phone width up, and colors switch automatically with the system dark-mode setting.
- **Brand.** Intrigue's colors (dark teal `#123A48`, teal `#1B5063`, lime `#9DC31B`, light blue `#CCE5ED`) are set as CSS variables, with Raleway as the typeface.

## Assumptions in the math

These are modeling choices. They are worth knowing before relying on any result.

**Financing**
- The mortgage is a fixed-rate, fully amortizing loan on the purchase price minus the down payment. A 0% rate is supported.
- Renovation costs and closing costs to buy are paid in cash up front, not financed.
- Total cash required = down payment + closing costs to buy + renovation cost.
- If the loan term is under 30 years, mortgage payments stop once the loan is paid off.

**Income and expenses**
- Growth rates for rent, taxes, insurance, HOA, utilities and miscellaneous expenses compound yearly, starting in Year 2.
- Vacancy is a percent of gross scheduled rent.
- Maintenance reserve is a percent of gross scheduled rent.
- Management fee is a percent of rent actually collected, after vacancy.
- Utilities are only the ones the owner pays while a tenant is in place, since vacancy already covers empty months.

**Property value and sale**
- Property value starts from the "value after renovation" and appreciates at a fixed yearly rate starting in Year 1. If that value is higher than the purchase price, the gap shows up as equity immediately.
- Cap rate is net operating income (NOI) divided by the property value at the start of that year.
- Net sale proceeds = equity minus selling costs, where selling costs are a percent of the sale price.

**Returns**
- IRR uses the up-front cash as the Year-0 outflow, then each year's cash flow, with sale proceeds added in the final year. It is solved by bisection and shows "n/a" when no rate in the search range solves it.
- ROI = (cumulative cash flow + net sale proceeds − cash invested) ÷ cash invested.
- Annualized return (APY) converts that ROI to a yearly rate.

**Tax items**
- Depreciation is straight-line over 27.5 years (U.S. residential) on the building share of price plus renovation. It is shown for information only.
- Depreciation and mortgage interest are not applied to cash flow. The calculator does not estimate income tax, depreciation recapture or capital gains.

**Not included**
- Inflation adjustment.
- Refinancing.
- Capital expenditures beyond the maintenance reserve.
- Changing interest rates.

**Example data**
- The defaults are an illustrative Indianapolis-area duplex, not a live listing.
- All results are estimates for education, not financial, tax or legal advice. The page says this in its footer.

## Known limitation: the contact form does not send anything

The "Talk to an agent" form validates its fields and shows a thank-you message, but **the submission is not delivered anywhere**. No email or lead record is created. Connect it to a form backend before going live (see below).

## What I'd do next with more time

1. **Make the contact form deliver leads.** Post it to a service such as Formspree, Netlify Forms or a CRM webhook. Add spam protection and include the deal inputs in the lead, so the agent sees the numbers the visitor ran.
2. **Shareable and saved scenarios.** Encode the inputs in the URL so a deal can be bookmarked or sent to an agent. Let users save and compare two or three deals side by side.
3. **Exports.** A printable PDF summary and a CSV or Excel download of the 30-year table.
4. **Tests for the financial math.** Unit tests for the payment, amortization, IRR and ROI functions against known spreadsheet results. Split the math into its own module so it can be tested.
5. **Deeper modeling.**
   - Optional after-tax cash flow and depreciation recapture on sale.
   - A refinance or BRRRR scenario.
   - Separate capital-expenditure reserves.
   - Sensitivity views, such as what happens if rent comes in 10% lower or the rate is 1 point higher.
6. **Smarter defaults.** Pre-fill taxes, rent and insurance from local Indianapolis-area data by ZIP code, with clear sourcing.
7. **Polish and reliability.**
   - Bundle Chart.js and the font locally so the page works offline and doesn't depend on CDNs.
   - Add analytics on which inputs people change.
   - Run a full screen-reader and Lighthouse audit.

## License

Add a license of your choice, or keep the repository private if this is proprietary to Intrigue Real Estate & Property Management.
