
# Task 3 - Integration Design

## OrthoNow Consultation Landing Page Integration

When a patient submits the consultation form, the landing page first pushes the `consultation_form_submitted` event to the Google Tag Manager (GTM) dataLayer. GTM then triggers the Google Ads Conversion Tag so that the conversion is recorded immediately for campaign optimization.

At the same time, the form data (Name, Phone, and Clinic Preference) is sent securely to a backend API using an HTTPS POST request. I would use a backend integration instead of calling HubSpot directly from the browser because API keys and access tokens should never be exposed on the client side.

The backend first searches HubSpot using the **CRM Search API** with the **Phone** property. This step is important because HubSpot's default deduplication works on **Email**, not Phone. Since this form only collects Name and Phone, searching by phone prevents duplicate contacts. If the phone number already exists, the existing contact is updated. Otherwise, a new contact is created using the **HubSpot CRM Contacts API**.

The contact is stored with the following properties:

- Name
- Phone
- Clinic Preference
- Source = Google Ads - Consultation Landing Page
- Lead Status = New Enquiry

After the HubSpot API returns a successful response, the backend publishes a message to a queue such as **RabbitMQ**. A worker service consumes the message and calls the **Namoza WhatsApp Business API (Karix)** to send the confirmation WhatsApp message. Using a queue makes the integration reliable and keeps the landing page fast.

## Biggest Failure Point

The biggest failure point is the HubSpot API becoming unavailable or timing out. If this happens, the lead should be stored in a queue or database and retried automatically using exponential backoff. All failures should be logged, and alerts should notify the team if retries continue to fail.

## WhatsApp SLA (Within 2 Minutes)

The WhatsApp message could be delayed because of HubSpot API failures, queue backlog, network issues, Karix API downtime, or rate limiting. To monitor this SLA, every request should include timestamps, processing duration, and delivery status. A monitoring dashboard such as Grafana or CloudWatch can track message delivery times, and alerts should be triggered if any message takes longer than 2 minutes.

## Phone Number Deduplication

HubSpot does not deduplicate contacts using phone numbers by default; it only deduplicates by email. Therefore, before creating a contact, the backend searches for an existing contact using the Phone property. If a matching phone number is found, the existing contact is updated instead of creating a new contact. This prevents duplicate patient records in the CRM.