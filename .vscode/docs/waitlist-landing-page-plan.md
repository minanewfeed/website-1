# Waitlist Landing Page Plan

## Overview
This document outlines the plan for creating a landing page where users can join a waitlist for early access. The page will be a simple form that collects name and email (required fields) and organization (optional field). The collected data will be sent to EmailOctopus.

## Technical Requirements
- Host on Netlify
- Integrate with EmailOctopus for email collection
- Responsive design to match the existing site aesthetics
- Form validation for required fields

## Page Structure

### HTML Structure
```html
<!doctype html>
<html lang="en" class="sr">
<head>
  <meta charset="utf-8">
  <title>Join the Waitlist | Feeliks Dynamics</title>
  <meta name="description" content="Join the waitlist for early access to our new product/service.">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <!-- OG and Twitter meta tags -->
  <!-- Favicon links -->
  <!-- Font declarations -->
  <!-- CSS links -->
</head>
<body>
  <!-- Header section -->
  
  <!-- Main content section -->
  <main>
    <section class="waitlist-section">
      <div class="container">
        <h1>Join Our Waitlist</h1>
        <p>Sign up to get early access when we launch.</p>
        
        <form id="waitlist-form" name="waitlist" method="POST" data-netlify="true" netlify-honeypot="bot-field">
          <input type="hidden" name="form-name" value="waitlist" />
          <p class="hidden">
            <label>Don't fill this out if you're human: <input name="bot-field" /></label>
          </p>
          
          <div class="form-group">
            <label for="name">Full Name *</label>
            <input type="text" id="name" name="name" required>
          </div>
          
          <div class="form-group">
            <label for="email">Email Address *</label>
            <input type="email" id="email" name="email" required>
          </div>
          
          <div class="form-group">
            <label for="organization">Organization</label>
            <input type="text" id="organization" name="organization">
          </div>
          
          <button type="submit" class="btn">Join Waitlist</button>
        </form>
      </div>
    </section>
  </main>
  
  <!-- Footer section -->
  
  <!-- JS scripts -->
</body>
</html>
```

### CSS Structure
The page will use the existing site styles with additional custom CSS for the form elements:

```css
/* Waitlist form styles */
.waitlist-section {
  padding: 5rem 0;
}

#waitlist-form {
  max-width: 500px;
  margin: 2rem auto;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
}

.form-group input {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.hidden {
  display: none;
}

/* Success message styles */
.success-message {
  padding: 1rem;
  background-color: #d4edda;
  color: #155724;
  border-radius: 4px;
  margin-top: 1rem;
  display: none;
}
```

## Netlify Forms Integration
The page will use Netlify Forms for initial data collection:

1. Add `data-netlify="true"` attribute to the form
2. Include a hidden input with name="form-name" value="waitlist"
3. Add a honeypot field to prevent spam

## EmailOctopus Integration
EmailOctopus integration can be handled in two ways:

### Option 1: Netlify Functions
Use a Netlify serverless function to forward form submissions to EmailOctopus API:

```javascript
// functions/submit-to-emailoctopus.js
const fetch = require('node-fetch');

exports.handler = async (event) => {
  const formData = JSON.parse(event.body);
  
  const emailOctopusData = {
    api_key: 'YOUR_EMAILOCTOPUS_API_KEY',
    email_address: formData.email,
    fields: {
      FirstName: formData.name,
      Organization: formData.organization || ''
    },
    tags: ['waitlist'],
    status: 'SUBSCRIBED'
  };

  try {
    const response = await fetch(
      'https://emailoctopus.com/api/1.6/lists/YOUR_LIST_ID/contacts',
      {
        method: 'POST',
        body: JSON.stringify(emailOctopusData),
        headers: { 'Content-Type': 'application/json' }
      }
    );
    
    const data = await response.json();
    
    if (data.error) {
      return {
        statusCode: 400,
        body: JSON.stringify({ message: data.error.message })
      };
    }
    
    return {
      statusCode: 200,
      body: JSON.stringify({ message: 'Success! You have been added to our waitlist.' })
    };
  } catch (error) {
    return {
      statusCode: 500,
      body: JSON.stringify({ message: 'Something went wrong. Please try again later.' })
    };
  }
};
```

### Option 2: Zapier Integration
Use Zapier to connect Netlify forms to EmailOctopus:

1. Set up a Zap with a Netlify forms trigger
2. Configure an EmailOctopus action to add contacts to your list
3. Map the form fields to EmailOctopus fields

## Client-side Form Handling
Add JavaScript for form validation and submission:

```javascript
document.addEventListener('DOMContentLoaded', () => {
  const form = document.getElementById('waitlist-form');
  const successMessage = document.querySelector('.success-message');
  
  if (form) {
    form.addEventListener('submit', async (e) => {
      // Netlify handles the form submission
      // This is just for additional client-side validation
      const nameInput = document.getElementById('name');
      const emailInput = document.getElementById('email');
      
      if (!nameInput.value || !emailInput.value) {
        e.preventDefault();
        alert('Please fill in all required fields.');
        return;
      }
      
      // Form submission is handled by Netlify
    });
  }
  
  // Check for success parameter in URL
  const urlParams = new URLSearchParams(window.location.search);
  if (urlParams.get('success') === 'true') {
    if (successMessage) {
      successMessage.style.display = 'block';
    }
    // Hide form if submission was successful
    if (form) {
      form.style.display = 'none';
    }
  }
});
```

## Implementation Tasks
1. Create prevena.html as the waitlist landing page
2. Add necessary CSS styles
3. Set up Netlify Forms in the HTML
4. Create a Netlify function for EmailOctopus integration
5. Add client-side JavaScript for form validation
6. Test the form submission flow
7. Set up redirects and success page handling

## Security Considerations
1. Use honeypot fields to prevent spam submissions
2. Keep EmailOctopus API keys secure in environment variables
3. Validate form inputs on both client and server side
4. Use HTTPS for all communications

## Testing Plan
1. Test form validation with valid and invalid inputs
2. Verify form submissions are captured in Netlify dashboard
3. Confirm data is properly sent to EmailOctopus
4. Test responsive design on multiple device sizes
5. Verify accessibility compliance

## Launch Checklist
1. Finalize page content and design
2. Set up proper redirects
3. Configure EmailOctopus list and fields
4. Deploy to Netlify with proper environment variables
5. Test the live form

## Next Steps
After approval of this plan, implementation can begin with the creation of the HTML file and setting up the Netlify deployment.
