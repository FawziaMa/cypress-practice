# Cypress Practice

# To Get Started
- Install `npm install cypress --save-dev`

# Test Runner Component
- Open Cyress with `npx cypress open`. Cypress directory is provided by the command. Test runner is also opened with the command.
- To run test cases, double click on a test file. A web tab will open executing all of the step in that particular test case. 

# Writing Test Case in Cypress
- Test Suite & Test Case Structure in Crypress(Mocha)
```js
describe("Test Suite", function(){
    // in each test suite, we can define multiple test cases using it() function
    it('test case 1', function(){
        // steps
    })

    it('test case 2', function(){
        // steps
    })
})
```
- Remove test files provided by Cypress within `integration/examples` before creating own test cases. Create `firsttest.js` file.
```js
// sample test
describe("First Test", () => {
    it("doesn't do much", () => {
        expect(true).to.equal(true)
    })
})
```

# How to Run Crypress Tests in Test Runner in Terminal
- Cypress supports the electron browser. Runs Electron by default. Can switch between Google Chrome and Electron in the test runner
- To run ALL the test under examples directory, `npx cypress run` in the terminal. Running `npx cypress run --headed` will run in the browser
- To run a SINGLE test, `npx cypress run  -spec "cypress/integration/examples/test_file.js"`
- To switch to Chrome from terminal, run the command, `npx cypress run --browser chrome`

# Folder Structure
- `Fixtures`, use to maintain the test data. Test cases, by default, will refer to this location for the test data.
- `Integration`, inside we have the `examples` directory which hold the test cases.
- `Plugins` contains the events listeners. Logs the events.
- `Screenshot` failure screenshots are stored
- `Support` contains reusable scripts
- `Videos` 
- `node_modules` dependencies installed 
- `cypress.json` Cypress configurations. Overide default values

# Locating Elements
- Look for elements inside cypress
- `get()` get one or more DOM elements by selector
```js
    // get the element
    cy.get(selector) // locate the element
```
- CSS selector `.class`, `#id`, `[attribute=value]`, `.class[attribute=value]`

# Locating Elements - Demo
1. Launch browser and Open URL `https://demo.nopcommerce.com/`
2. Enter Text in Search box "Apple Macbook Pro 13-inch"
3. Click on `Search` Button
4. Click on `Add to cart`
5. Provide Quantity 2
6. Click on `Add to cart`
7. Click on `Shopping Cart` Link at the top of the page
8. Verify the total amount

```js
// <reference types="cypress"/> // get auto solutions/methods from cypress

describe('Locating Element', () => {
    it('Verify types of locators', () => {
        cy.visit("https://demo.nopcommerce.com/")
        // get search bar. and type the search term
        cy.get("#small-searchterms").type("Apple MacBook Pro 13-inch")
        // grab the submit search button
        cy.get("[type='submit']").click() // click on the search button
        // add to cart
        cy.get(".product-box-add-to-cart-button[value='Add to cart']").click()

        // clear the preb value and type in a new one
        cy.get("#product_enteredQuantity_4").clear().type('2') //quantity

        cy.get("#add-to-cart-button-4").click()// add to cart after setting quantity

        cy.wait(5000)
        // go to shopping cart
        cy.get("#topcartlink > a > span.cart-label").click()
        cy.wait(3000)

        // directed to shopping total. Grab the total price
        cy.get(".product-unit-price").contains('$1,800.00') //validating point
    })
})
```

# Interacting with UI Elements
- UI elements: `input box`, `radio buttons`
- Commands: `visit()`, `url()`, `get()`, `title()`
```js
describe('UI Elements', () => {
    it("Verify InputBox & Radio Buttons", () => {
        // cy.visit("http://newtours.demoaut.com/")
        cy.visit("http://demo.guru99.com/test/newtours/")
        // check url of site and verifies it includes "newtours"
        cy.url().should('include', 'newtours')
        // verify conditional for username input
        cy.get("input[name=userName]").should('be.visible').should('be.enabled').type("mercury")
        cy.get("input[name=password]").should('be.visible').should('be.enabled').type("mercury")
        cy.get("input[name=submit]").should('be.visible').click() // submit button
        
        cy.wait(1000)

        // click the flights tab after logging in
        cy.get("body > div:nth-child(5) > table > tbody > tr > td:nth-child(1) > table > tbody > tr > td > table > tbody > tr > td > table > tbody > tr:nth-child(2) > td:nth-child(2) > a").click()

        // title verification
        cy.title().should('eq', 'Find a Flight: Mercury Tours:')

        // Radio Buttons
        cy.get('input[value=roundtrip]').should('be.visible').should('be.checked') //visible and checked verification
        cy.get('input[value=oneway]').should('be.visible').should('not.be.checked').click() 

        // continue button
        cy.get('[name=findFlights]').should('be.visible').click()

        // verify title after redirect, checks if font includes No Seats Available
        cy.get("body > div:nth-child(5) > table > tbody > tr > td:nth-child(2) > table > tbody > tr:nth-child(4) > td > table > tbody > tr:nth-child(1) > td:nth-child(2) > table > tbody > tr:nth-child(1) > td > p > font > b > font:nth-child(1)").contains("No Seats Avaialble")
    })
})
```

- UI Elements: `check boxes`, `drop downs`
- Commands: `check`, `uncheck`. `type`
```js
// <reference types="cypress"/> // get auto solutions/methods from cypress

describe('UI Elements Part 2', () => {
    it("Hobbies Check Box", () => {
        cy.visit('http://demo.automationtesting.in/Register.html')

        // check first checkbox and it shoudl have a avalue of Cricket
        cy.get('#checkbox1').check().should('be.checked').and('have.value', 'Cricket')
        cy.get('#checkbox2').check().should('be.checked').and('have.value', 'Movies')
        cy.get('#checkbox3').check().should('be.checked').and('have.value', 'Hockey')

        // uncheck
        cy.get('#checkbox1').uncheck().should('not.be.checked')
        cy.get('#checkbox2').uncheck().should('not.be.checked')
        cy.get('#checkbox3').uncheck().should('not.be.checked')
        
        // get all input when type is checkbox and pass in array of Values we want to check
        cy.get('input[type=checkbox]').check(['Cricket', 'Hockey'])
    })

    it("Skills Drop Down", () => {
        // url can still be visited from previous it block
        //select the select drop down of skills and select Android and test if value is the same
        cy.get("#Skills").select('Android').should('have.value', 'Android')
    })

    it("Languages Multi Select", () => {
        // url can still be visited from previous it block
        // get element that brings down the selection
        cy.get('#msdd').click()
        // get element that contains the slections once open. Multi select
        cy.get('.ui-corner-all').contains('English').click()
        cy.get('.ui-corner-all').contains('Japanese').click()
    })

    it("Select countries searchable drop down", () => {
        // url can still be visited from previous it block
        // Click the select country option to bring down the options and input field. Forcing it since a dropdown is covering the clickable element
        cy.get('[role=combobox]').click({force:true})

        // get the input field by class and type Ind
        cy.get('.select2-search__field').type('Ind')
        //press enter
        cy.get('.select2-search__field').type('{enter}')
    })
})
```

# Interacting with UI Elements
- UI Elements: `alerts`
- Commands: `.on`
```js
describe('Alert test', () => {
    it('Alerts', () => {
        // cy.visit('https://www.seleniumeasy.com/test/javascript-alert-box-demo.html')
        // cy.get('#easycont > div > div.col-md-6.text-left > div:nth-child(4) > div.panel-body > button').click()
        // // on cypress, there will be no alert window notification. Automatically closes but it shows alert

        // // run an event to captjure the message in the alert window
            // cy.on('window:alert', (str) => {
            //     // verify assertions
            //     expect(str).to.equal('I am an alert box!')
            // })

        cy.visit('http://testautomationpractice.blogspot.com/')
        cy.get('#HTML9 > div.widget-content > button').click()
            //confirmation alert window
            cy.on('window:confirm', (str) => {
                // verify assertions
                expect(str).to.equal('Press a button!')
            })
    })
})

```

# Navigating Pages
- Navigate back or forward to the previous or next URL in the browser's history
- Commands: `go()`

# Handling Web Table
- Check value presence anywhere in the table
- check value presence in specific row and column
- check value presence based on condition by iterating rows
```js
describe('Table test', () => {
    it('Table Test', () => {

        cy.visit(`http://testautomationpractice.blogspot.com/`)
        // Check value presence anywhere in the table. There's multiple elements with TD. In contains, specify which tag
        cy.get('table[name=BookTable]').contains('td', 'Learn Selenium').should('be.visible')

        // check value presence in specific row and column
        cy.get('table[name=BookTable] > tbody > tr:nth-child(2) > td:nth-child(3)').contains('Selenium').should('be.visible')

        // check value presence based on condition by iterating rows, look for author, then find t's book title
        // find all tds. use .each to iterate over each one $ variables
        cy.get('table[name=BookTable] > tbody > tr td:nth-child(2)').each(($e,index, $list) => {
            const text = $e.text()
            if(text.includes('Amod')){
                // get first column but point to the same index it is found in row
                cy.get('table[name=BookTable] > tbody > tr td:nth-child(1)').eq(index).then((bookname) => {
                    // bookname is the argument from the element found. We call .text() to pull out the information inside the element
                    const book = bookname.text()
                    expect(book).to.equal("Master In Java")
                })
            }
        }) 

    })
})
```

# Cypress Hooks
- Cypress hooks borrowed from Mocha used to organize tests
- Commands: `beforeEach`, `afterEach`, `before`, `after`
- `beforeEach` that particular block will execute before each test starts
- `afterEach` will execute after each test completes
- `before` Before doing all tests, can specifiy in a block which will execute once before.
- `after` After doing all test, can specificy in a block which will execute once after
```js
describe('Hooks', () => {
    // before executing all three tests, execute method login, multiple times before each it block
    before(() => {
        //runs once before all tests in the block
        cy.log('*************** This is a SETUP block ********************')
    })

    after(() => {
        //run once after all tests in the block
        cy.log('*************** This is a TEAR DOWN block ********************')
    })

    beforeEach(() => { 
        //runs before each test in the block
        cy.log('*************** This is a LOGIN block ********************')
    })

    afterEach(() => {
        //runs after each test in the block
        cy.log('*************** This is a LOGOUT block ********************')
    })

    it('Searching', () => {
        cy.log('************ This is Searching Test')
    })

    it('Advanced Searching', () => {
        cy.log('************ This is Advanced Searching Test')
    })

    it('Listing Products', () => {
        cy.log('************ This is Listing Products Test')
    })
})
```

# Cypress Fixture
- Load a fixed set of data in a file
- Inside the directory, `fixtures` provided by cypress, we can create and provide multiple files
