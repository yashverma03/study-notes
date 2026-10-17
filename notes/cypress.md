# Intro

e2e test

```javascript
describe('🧪 Auth Flow E2E Tests', () => {
  // Runs once before all tests
  before(() => {
    // Can be used to set up initial DB state or seed data via API
    cy.log('Before all tests');
  });

  // Runs before each individual test
  beforeEach(() => {
    // Visit the login page before every test
    cy.visit('http://localhost:3000/login');
  });

  // Runs after each test
  afterEach(() => {
    // Clear cookies/local storage (optional)
    cy.clearCookies();
    cy.clearLocalStorage();
  });

  // Runs once after all tests are complete
  after(() => {
    cy.log('After all tests');
  });

  it('should show login form with email and password', () => {
    // Get elements by their name or label
    cy.get('input[name="email"]').should('exist');
    cy.get('input[name="password"]').should('exist');
    cy.get('button[type="submit"]').should('contain', 'Login');
  });

  it('should display error on invalid credentials', () => {
    // Fill inputs and click
    cy.get('input[name="email"]').type('wrong@example.com');
    cy.get('input[name="password"]').type('wrongpass');
    cy.get('button[type="submit"]').click();

    // Assert error message
    cy.contains('Invalid email or password').should('be.visible');
  });

  it('should login with correct credentials and redirect to dashboard', () => {
    cy.get('input[name="email"]').type('test@example.com');
    cy.get('input[name="password"]').type('password123');
    cy.get('button[type="submit"]').click();

    // Confirm redirect and welcome text
    cy.url().should('include', '/dashboard');
    cy.contains('Welcome, Test User').should('be.visible');
  });

  it('should fetch and display user profile using API call', () => {
    // Mocking GET request using cy.intercept
    cy.intercept('GET', '/api/profile', {
      statusCode: 200,
      body: { name: 'Test User', email: 'test@example.com' },
    }).as('getProfile');

    // Trigger the call (navigate or button click)
    cy.visit('http://localhost:3000/profile');

    // Wait for the API call to finish
    cy.wait('@getProfile');

    // Validate UI displays mocked data
    cy.contains('Test User').should('be.visible');
    cy.contains('test@example.com').should('be.visible');
  });

  it('should POST login API call and get 200 response', () => {
    cy.request({
      method: 'POST',
      url: 'http://localhost:3000/api/login',
      body: {
        email: 'test@example.com',
        password: 'password123',
      },
    }).then((response) => {
      expect(response.status).to.eq(200);
      expect(response.body).to.have.property('token');
    });
  });
});
```

Component test

```jsx
import React from 'react';
import { Button } from './Button';

describe('🧪 <Button /> Component Tests with spy()', () => {
  beforeEach(() => {
    cy.log('Rendering Button component...');
  });

  afterEach(() => {
    cy.log('Button test completed.');
  });

  it('should render with correct label', () => {
    cy.mount(<Button label="Click Me" />);
    cy.get('[data-testid="my-button"]').should('contain', 'Click Me');
  });

  it('should call onClick using cy.spy() when clicked', () => {
    // Create a plain function and wrap it with spy
    const handleClick = () => {};
    const spy = cy.spy(handleClick).as('clickSpy');

    // Mount with spy
    cy.mount(<Button label="Spy Test" onClick={spy} />);

    // Click the button
    cy.get('[data-testid="my-button"]').click();

    // Assert that the spy was called
    cy.get('@clickSpy').should('have.been.calledOnce');
  });
});
```
