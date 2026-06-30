
# Task 1 - GTM Event Schema for OrthoNow

## Overview

This document defines the Google Tag Manager (GTM) event tracking plan for OrthoNow's website. The goal is to capture key user interactions, measure the appointment booking funnel, and provide accurate conversion data to Google Analytics 4 (GA4) and Google Ads.

---

## 1. GTM Event Schema Table


| Event Name | Trigger Type | Key Parameters | GA4 Report / Audience |
|------------|--------------|----------------|------------------------|
| booking_step_complete | Custom Event (dataLayer Push) | step_number, clinic_location, specialty | Funnel Exploration |
| booking_completed | Form Submission | booking_id, clinic_location, specialty | Conversions |
| call_now_click | Click Trigger | phone_number, page_name, clinic_location | Engagement Report |
| whatsapp_click | Click Trigger | page_name, clinic_location, source | Engagement Report |
| patient_guide_download | Form Submission + File Download | patient_name, phone_number, guide_name | Downloads Report |
| clinic_page_view | Page View | clinic_name, city, page_url | Pages & Screens Report |
| blog_scroll_50 | Scroll Trigger (50%) | article_title, scroll_percentage, category | Engagement Report |
| blog_scroll_90 | Scroll Trigger (90%) | article_title, scroll_percentage, category | Engaged Users Audience |


## 2. Booking Form Funnel Tracking

### Step 1 - Location & Specialty Selection

```json
{
  "event": "booking_step_complete",
  "step_number": 1,
  "step_name": "location_specialty_selected",
  "clinic_location": "Bengaluru",
  "specialty": "Orthopaedics"
}
```

### Step 2 - Patient Details

```json
{
  "event": "booking_step_complete",
  "step_number": 2,
  "step_name": "patient_details_entered",
  "patient_name": "John Doe",
  "phone_number": "9876543210",
  "preferred_date": "2026-07-05"
}
```

### Step 3 - Booking Confirmation

```json
{
  "event": "booking_completed",
  "step_number": 3,
  "booking_status": "success",
  "clinic_location": "Bengaluru",
  "specialty": "Orthopaedics"
}
```
---

## 3. Funnel Drop-off Tracking

Each step of the appointment booking form pushes a custom `booking_step_complete` event to the dataLayer. Google Tag Manager captures these events and sends them to Google Analytics 4 (GA4).

These events are triggered by custom dataLayer pushes implemented by the front-end developer, and GTM listens to these events before sending them to GA4.

Using GA4 Funnel Exploration, we can monitor how many users complete Step 1, Step 2, and Step 3. This helps identify where users drop off during the booking process, allowing the team to optimize the form and improve conversion rates.

Example Funnel:

Step 1 → Select Clinic & Specialty → 100 Users

↓

Step 2 → Enter Patient Details → 75 Users

↓

Step 3 → Booking Confirmed → 55 Users

This funnel clearly shows the percentage of users leaving at each stage of the booking process.

---

## 4. Google Ads Conversion

The `booking_completed` event should be imported as the primary Google Ads conversion because it represents a successful appointment booking, which is the main business goal.

Other events such as `call_now_click` and `whatsapp_click` indicate user interest but do not confirm a completed consultation request. Optimizing campaigns based on `booking_completed` will provide higher-quality leads and better advertising performance.
