# CLAUDE.md - AI Assistant Guide for VvE Verduurzamings Calculator

## Project Overview

**Project Name**: VvE Verduurzamings Calculator 2025
**Type**: Single File Application (SFA)
**Primary Language**: JavaScript (React via CDN)
**Framework**: React 18 + Tailwind CSS
**Purpose**: Interactive financial calculator for Dutch homeowner associations (VvE) to evaluate sustainability improvements

### What This Tool Does

This calculator helps VvE boards and members understand the financial implications of sustainability measures (insulation, window upgrades, etc.) by:
- Calculating construction costs with BTW (21% VAT)
- Computing national (SVVE) and municipal subsidies
- Determining loan requirements and monthly costs via Warmtefonds
- Estimating energy savings from gas reduction
- Providing net monthly impact per apartment

## Repository Structure

```
VVE-Calculator/
├── index.html              # Complete application (HTML + CSS + JS)
├── README.md               # User-facing documentation
├── VERANTWOORDING.md       # Technical calculation methodology
└── CLAUDE.md               # This file - AI assistant guide
```

### File Responsibilities

- **index.html**: Contains ALL application logic, UI, and styling. This is a deliberate architectural choice for portability and simplicity.
- **README.md**: User documentation in Dutch explaining features, installation, and disclaimers.
- **VERANTWOORDING.md**: Technical documentation detailing formulas, constants, and calculation methodology for transparency.

## Architecture

### Single File Application (SFA) Pattern

**Key Principle**: Everything in one file for easy sharing and no-build deployment.

**Technology Stack**:
```html
<!-- React & ReactDOM via unpkg CDN -->
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>

<!-- Babel Standalone for JSX transformation in browser -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<!-- Tailwind CSS via CDN -->
<script src="https://cdn.tailwindcss.com"></script>

<!-- Lucide Icons -->
<script src="https://unpkg.com/lucide@latest"></script>
```

**Why This Architecture?**
1. No build process required
2. No npm dependencies to manage
3. Easy to share (single HTML file)
4. Works directly in any modern browser
5. Ideal for GitHub Pages hosting

### Component Structure

The application is a single React component (`App`) with:

```javascript
App (Main Component)
├── Constants (DATA, EST_*, INTEREST_RATE, etc.)
├── State Management (inputs, choices, results)
├── Calculation Logic (useEffect hook)
├── Event Handlers (handleInputChange, handleChoiceChange)
├── CSV Export Function (exportToCSV)
└── UI Rendering (JSX)
    ├── Header with Export Button
    ├── Left Column: Input Controls
    │   ├── Building Data (apartments, m²)
    │   ├── Advice Costs (DMJOP, procesbegeleiding)
    │   ├── Municipal Subsidy Eligibility
    │   └── Measure Selection (glass, roof, facade)
    └── Right Column: Results Display
        ├── Investment Summary Cards
        ├── Subsidy Breakdown
        ├── Cost Details
        └── Monthly Impact Calculator
```

## Core Data Model

### Constants (Lines 34-61 in index.html)

```javascript
// Measure configurations with costs, subsidies, and savings
const DATA = {
    glass: {
        none: { label: 'Geen wijziging', cost: 0, sub: 0, savings: 0 },
        hr: { label: 'HR++ Glas', cost: 140, sub: 50, savings: 15 },
        triple: { label: 'Triple Glas + Nieuw Kozijn', cost: 850, sub: 222, savings: 35 }
    },
    roof: { /* similar structure */ },
    facade: { /* similar structure */ }
};

// Estimation factors per apartment
const EST_GLASS_M2 = 12;   // Average window area per unit
const EST_ROOF_M2 = 25;    // Average roof share per unit
const EST_FACADE_M2 = 35;  // Average facade area per unit

// Financial constants
const INTEREST_RATE = 0.0369;              // Warmtefonds loan rate (3.69%)
const LOAN_DURATION = 20;                   // Years
const GAS_PRICE = 1.35;                     // EUR per m³
const BTW = 1.21;                           // 21% VAT multiplier
const MUNICIPAL_SUBSIDY_PER_APT = 1000;    // Municipal subsidy amount
```

### State Structure

```javascript
// User Inputs
inputs: {
    apartments: number,                // Number of units in building
    sqmGlass: number,                  // Total glass area (m²)
    sqmRoof: number,                   // Total roof area (m²)
    sqmFacade: number,                 // Total facade area (m²)
    currentServiceCosts: number,       // (Currently unused)
    maintenanceBudget: number,         // MJOP reserve to invest (EUR)
    costDMJOP: number,                 // DMJOP creation cost (ex VAT)
    costAdvice: number,                // Process guidance cost (ex VAT)
    builtBefore1995: boolean,          // Municipal subsidy eligibility
    twoPoorPartsPresent: boolean,      // Municipal subsidy requirement
    wozEligibleApartments: number      // Units with WOZ < €486k
}

// User Choices
choices: {
    glass: 'none' | 'hr' | 'triple',
    roof: 'none' | 'base',
    facade: 'none' | 'spouw' | 'outer'
}

// Calculated Results
results: {
    investGross: number,               // Total gross investment
    investConstruction: number,        // Construction costs only
    investAdvice: number,              // Advice costs with VAT
    svveSubsidy: number,              // National SVVE subsidy
    municipalSubsidy: number,          // Local municipal subsidy
    totalSubsidy: number,              // Sum of all subsidies
    investNet: number,                 // Net financing needed
    loanMonthly: number,               // Monthly loan cost per apartment
    energySavingMonthly: number,       // Monthly energy savings per apartment
    netMonthlyEffect: number,          // Net monthly impact
    measureCount: number               // Number of active measures
}
```

## Calculation Flow

The main calculation happens in the `useEffect` hook (lines 87-139):

### Step 1: Construction Costs
```javascript
// Calculate costs per measure (with 21% BTW)
costGlass = sqmGlass × DATA.glass[choice].cost × BTW
costRoof = sqmRoof × DATA.roof[choice].cost × BTW
costFacade = sqmFacade × DATA.facade[choice].cost × BTW

// Add advice costs and contingency
adviceCosts = (costDMJOP + costAdvice) × BTW
unforeseen = (costGlass + costRoof + costFacade) × 0.05
totalGross = costGlass + costRoof + costFacade + unforeseen + adviceCosts
```

### Step 2: Subsidies
```javascript
// SVVE subsidy (per m²)
svveSubsidy = (sqmGlass × DATA.glass[choice].sub) +
              (sqmRoof × DATA.roof[choice].sub) +
              (sqmFacade × DATA.facade[choice].sub)

// Municipal subsidy (conditional)
if (builtBefore1995 && twoPoorPartsPresent && activeMeasures >= 1) {
    municipalSubsidy = wozEligibleApartments × 1000
}

totalSubsidy = svveSubsidy + municipalSubsidy
```

### Step 3: Financing
```javascript
// Net amount to finance
netInvestment = totalGross - totalSubsidy - maintenanceBudget

// Annuity calculation (if financing needed)
if (netInvestment > 0) {
    monthlyRate = INTEREST_RATE / 12
    numPayments = LOAN_DURATION × 12
    annuityFactor = (r × (1+r)^n) / ((1+r)^n - 1)
    monthlyTotal = netInvestment × annuityFactor
    monthlyPerApartment = monthlyTotal / apartments
}
```

### Step 4: Energy Savings
```javascript
// Total gas savings (m³)
totalSavingsM3 = (sqmGlass × DATA.glass[choice].savings) +
                 (sqmRoof × DATA.roof[choice].savings) +
                 (sqmFacade × DATA.facade[choice].savings)

// Convert to EUR and monthly per apartment
totalSavingEuro = totalSavingsM3 × GAS_PRICE
monthlySavingPerApp = totalSavingEuro / 12 / apartments
```

### Step 5: Net Effect
```javascript
netMonthlyEffect = monthlyLoanCostPerApp - monthlySavingPerApp
// Positive = costs exceed savings (increase in service costs)
// Negative = savings exceed costs (decrease in service costs)
```

## Development Workflows

### Making Changes to the Application

Since this is a single-file application, all changes are made in `index.html`.

#### Modifying Constants (Common Task)

**Location**: Lines 34-61 in index.html

**Example Task**: Update gas price for 2026

```javascript
// Find this constant
const GAS_PRICE = 1.35;

// Update to new value
const GAS_PRICE = 1.45; // Updated for 2026
```

**Example Task**: Update interest rate

```javascript
const INTEREST_RATE = 0.0369; // Change to current Warmtefonds rate
```

**Example Task**: Modify subsidy amounts

```javascript
// In DATA object, update subsidy (sub) values
const DATA = {
    glass: {
        hr: { label: 'HR++ Glas', cost: 140, sub: 60, savings: 15 }, // Changed from 50 to 60
        // ...
    }
}
```

#### Adding a New Measure Type

1. Add option to appropriate DATA category
2. Update UI select dropdown (auto-generated from DATA)
3. Update VERANTWOORDING.md with new calculation details

#### Modifying UI Layout

- **Styling**: Uses Tailwind CSS utility classes
- **Icons**: Lucide icon set via `<Icon name="icon-name" />`
- **Colors**: Follows semantic color scheme (blue=primary, green=positive, orange/red=costs)

### Testing Changes

**Manual Testing Checklist**:
1. Open index.html in browser (Chrome, Edge, Safari recommended)
2. Change apartment count - verify m² updates automatically
3. Select different measure combinations
4. Check subsidy eligibility logic (toggle checkboxes)
5. Verify CSV export downloads correctly
6. Test with edge cases:
   - 0 apartments
   - No measures selected
   - All measures selected
   - Municipal subsidy ineligible

**Regression Testing**:
- Compare results with known scenarios documented in issues
- Verify formulas match VERANTWOORDING.md specifications

### Deployment to GitHub Pages

**Current Status**: Repository appears to be on branch `claude/claude-md-miua6jhoqr3t9bks-01Y5ZeTji6gS19FiiR9s38DP`

**Steps to Deploy**:
1. Merge changes to main branch
2. GitHub Settings → Pages
3. Source: Deploy from branch `main`
4. Root directory
5. Save and wait for deployment
6. Update README.md with live URL

**No Build Required**: GitHub Pages serves index.html directly.

## Code Conventions

### Language
- **UI Text**: Dutch (target audience is Dutch VvE boards)
- **Code**: English variable names where appropriate, Dutch for domain terms
- **Comments**: Dutch acceptable for domain-specific explanations

### Naming Conventions
```javascript
// Constants: UPPER_SNAKE_CASE
const GAS_PRICE = 1.35;

// State variables: camelCase
const [inputs, setInputs] = useState({});

// Functions: camelCase with descriptive names
const handleInputChange = (e) => {};
const formatCurrency = (val) => {};

// Component: PascalCase
const App = () => {};
```

### Formatting
- **Indentation**: 4 spaces (consistent throughout file)
- **Line Length**: Generally kept under 120 characters
- **JSX**: Attributes on new lines for complex elements

### Data Flow
- **Unidirectional**: User inputs → State → useEffect calculation → Results display
- **No prop drilling**: Single component architecture avoids this
- **Automatic recalculation**: useEffect dependency array triggers on any input/choice change

## Important Technical Considerations

### Currency Formatting
```javascript
const formatCurrency = (val) => {
    return new Intl.NumberFormat('nl-NL', {
        style: 'currency',
        currency: 'EUR'
    }).format(val);
};
```
Always uses Dutch locale for comma as decimal separator.

### BTW (VAT) Handling
- **Input costs**: Always stored excluding BTW
- **Calculation**: BTW applied during calculation (× 1.21)
- **Display**: Always shows final amount including BTW

### Municipal Subsidy Logic
**Critical**: Must meet ALL conditions:
1. `builtBefore1995 === true`
2. `twoPoorPartsPresent === true`
3. At least 1 measure selected (not 'none')
4. Only counts apartments with WOZ < €486,000

### CSV Export
- Uses UTF-8 encoding with BOM for Excel compatibility
- Dutch formatting (comma separators)
- Includes all key inputs and results
- Filename: `VvE_Verduurzaming_Detail.csv`

## Common AI Assistant Tasks

### 1. Updating Financial Constants

**When**: Annual updates, rate changes, new regulations

**Process**:
1. Locate constants in lines 34-61
2. Update relevant values (GAS_PRICE, INTEREST_RATE, subsidy amounts)
3. Update VERANTWOORDING.md to reflect changes
4. Update README.md if user-facing impact
5. Test calculations with example scenarios

### 2. Adding New Subsidy Rules

**Example**: New provincial subsidy layer

**Steps**:
1. Add new input fields in `inputs` state for eligibility criteria
2. Add UI controls in left column section
3. Create calculation logic in useEffect
4. Add to results breakdown display
5. Include in totalSubsidy calculation
6. Update CSV export to include new subsidy
7. Document in VERANTWOORDING.md

### 3. Modifying Estimation Factors

**When**: User feedback indicates estimates are inaccurate

**Location**: Lines 53-55
```javascript
const EST_GLASS_M2 = 12;
const EST_ROOF_M2 = 25;
const EST_FACADE_M2 = 35;
```

**Impact**: Changes automatic m² calculation when apartment count changes

### 4. UI/UX Improvements

**Approach**:
- Maintain two-column layout (inputs left, results right)
- Follow existing color semantics
- Keep responsive design (grid-cols-1 lg:grid-cols-12 pattern)
- Use Lucide icons for consistency
- Add tooltips/help text for complex fields

### 5. Bug Fixes

**Common Issues**:
- **Negative loan amounts**: Check if subsidies exceed costs
- **NaN results**: Verify division by zero protection
- **Incorrect subsidies**: Review eligibility logic
- **Export formatting**: Test with Dutch locale settings

**Debugging**:
- Add console.log statements in useEffect
- Check state updates with React DevTools (if installed)
- Verify constant values match VERANTWOORDING.md

## Git Workflow

### Branch Naming
- Feature branches: `feature/description`
- Bug fixes: `fix/description`
- AI assistant branches: `claude/session-id` (as per current branch)

### Commit Messages
- Use clear, descriptive messages in Dutch or English
- Reference specific changes to constants/calculations
- Examples:
  - "Update gas price to €1.45 for 2026"
  - "Fix municipal subsidy eligibility logic"
  - "Add tooltip for WOZ criteria"

### Pull Request Process
1. Create PR from feature branch to main
2. Include description of changes
3. Note any VERANTWOORDING.md updates
4. Test manually before merging

## Key Files Reference

### index.html Structure Map

```
Lines 1-23:    HTML head, CDN imports
Lines 24-32:   Icon component wrapper
Lines 33-61:   Constants and configuration (← MODIFY HERE for rates/prices)
Lines 64-76:   Initial state (default values)
Lines 78-82:   User choices state
Lines 84:      Results state
Lines 87-139:  Main calculation logic (← UNDERSTAND THIS for formula changes)
Lines 141-160: Input change handlers
Lines 162-164: Choice change handler
Lines 166-201: CSV export function
Lines 203-205: Currency formatter
Lines 207-461: JSX render (UI layout)
Lines 463-464: React render call
```

### When to Update Each File

**index.html**:
- Application logic changes
- UI/UX improvements
- Constant updates
- New features

**README.md**:
- User-facing documentation
- Feature descriptions
- Installation instructions
- Disclaimers

**VERANTWOORDING.md**:
- Formula changes
- Calculation methodology
- Source/reference updates
- Assumption changes

**CLAUDE.md** (this file):
- Architecture changes
- New conventions
- AI assistant guidance updates

## Formula Reference Quick Guide

For detailed formulas, see VERANTWOORDING.md. Quick reference:

| Calculation | Formula | Location |
|-------------|---------|----------|
| Gross Investment | `(ΣConstructionCosts × 1.05) + AdviceCosts` | Line 94 |
| SVVE Subsidy | `Σ(m² × subsidyRate)` | Lines 96-98 |
| Municipal Subsidy | `eligibleApts × €1000` (if conditions met) | Line 107 |
| Net Investment | `Gross - Subsidies - MJOPbudget` | Line 110 |
| Monthly Loan | `P × r(1+r)^n / ((1+r)^n - 1)` | Lines 112-115 |
| Energy Savings | `Σ(m² × savingsRate) × gasPrice / 12 / apts` | Lines 118-124 |
| Net Effect | `LoanCost - EnergySavings` | Results display |

## Validation Rules

### Input Validation
- Apartments: Must be > 0
- Square meters: Should be > 0 (auto-calculated but can override)
- WOZ eligible apartments: Cannot exceed total apartments
- Costs: Should be ≥ 0

### Business Logic Validation
- If no measures selected → netMonthlyEffect should be 0
- If investNet ≤ 0 → no loan needed, monthlyLoanCost = 0
- Municipal subsidy requires: bouwjaar<1995 AND 2 poor parts AND ≥1 measure

## Performance Considerations

**Current Performance**: Excellent
- Single file loads fast
- No API calls
- All calculations client-side
- React handles re-renders efficiently

**If Scaling Needed** (not currently necessary):
- Consider memoization for complex calculations
- Add loading states for exports
- Implement input debouncing if performance degrades

## Accessibility Notes

**Current State**: Basic accessibility
- Semantic HTML structure
- Form labels present
- Color contrast generally good

**Improvements to Consider**:
- Add ARIA labels to custom select dropdowns
- Ensure keyboard navigation works throughout
- Add skip links for screen readers
- Test with actual screen reader software

## Security Considerations

**Current Risk Level**: Very Low
- No backend/database
- No user authentication
- No data storage
- No external API calls
- Client-side only calculations

**Best Practices Maintained**:
- No eval() or dangerous code execution
- No XSS vulnerabilities (React handles escaping)
- CSV export uses Blob API safely

## Browser Compatibility

**Tested/Supported**:
- Chrome/Edge (Chromium) - Primary target
- Safari - Supported
- Firefox - Should work (test if issues reported)

**Requirements**:
- ES6+ support
- React 18 compatible
- Tailwind CSS via CDN support

**Not Supported**:
- Internet Explorer (deprecated)
- Very old mobile browsers

## Troubleshooting Guide

### Icons Not Showing
**Cause**: Lucide library not initialized
**Check**: Line 28 `lucide.createIcons()` call

### Calculations Wrong
**Steps**:
1. Verify constants match VERANTWOORDING.md
2. Check state updates in React DevTools
3. Add console.log in useEffect to trace calculation
4. Verify BTW multiplication (should be × 1.21)

### CSV Export Not Working
**Common Issues**:
- File encoding (should be UTF-8)
- Browser blocking download (check permissions)
- Filename special characters

### Responsive Layout Broken
**Check**:
- Tailwind classes: `lg:grid-cols-12` vs `grid-cols-1`
- Viewport meta tag present (line 5)
- No custom CSS overriding Tailwind

## Future Enhancement Ideas

These are documented for future consideration but NOT currently implemented:

1. **Scenario Comparison**: Save and compare multiple scenarios
2. **Print-Friendly View**: Optimized print stylesheet
3. **Advanced Settings Panel**: Hide complexity but allow power users to tweak
4. **Historical Data**: Track changes over multiple years
5. **Multi-Language Support**: English translation for wider audience
6. **Offline PWA**: Service worker for offline use
7. **Integration APIs**: Connect to government subsidy databases
8. **Amortization Schedule**: Show full 20-year payment breakdown

## Questions for Human Developers

If you're an AI assistant and encounter ambiguity, ask humans about:

1. **Source of Truth**: If constants conflict between code and VERANTWOORDING.md, which is correct?
2. **Rounding**: Should financial calculations round at each step or only at display?
3. **Municipal Subsidy**: Confirm eligibility rules with latest municipality regulations
4. **Gas Price**: Should this be user-configurable or remain constant?
5. **Edge Cases**: What should happen if subsidies exceed costs? (Currently allows negative loan)

## Related Documentation

- **VERANTWOORDING.md**: Mathematical formulas and methodology
- **README.md**: User-facing feature documentation
- **Dutch Government Sources**:
  - SVVE regulations: RVO.nl
  - Warmtefonds: nationaalwarmtefonds.nl
  - Municipal subsidies: Local gemeente websites

## Version History

- **2025.1**: Current version (as per VERANTWOORDING.md header)
- Includes DMJOP costs, process guidance, and municipal subsidy logic

---

## Quick Start for AI Assistants

1. **Read this file first** to understand architecture
2. **Read VERANTWOORDING.md** to understand calculations
3. **Read index.html lines 34-139** to see implementation
4. **Make changes** primarily in index.html
5. **Update documentation** in VERANTWOORDING.md if formulas change
6. **Test manually** by opening index.html in browser
7. **Commit with descriptive messages**

## Contact & Contribution

This project appears to be maintained by **bdrooai-sketch** (GitHub username inferred from branch).

For questions about the codebase, refer to:
1. This CLAUDE.md file for architecture
2. VERANTWOORDING.md for calculation details
3. Git history for recent changes
4. Create issues for bugs or enhancement requests

---

**Last Updated**: 2025-12-06
**Document Version**: 1.0
**Target Audience**: AI Assistants (Claude, GPT, etc.) working with this codebase
