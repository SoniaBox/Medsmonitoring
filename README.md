# Medsmonitoring
Medication Monitoring Tool

This README-style PRD is designed for an AI assistant to guide the build of a medication monitoring tool for prescription clerks in general practice. It outlines Key Features, Technical Stack (with pros and cons), Steps to Implementation, and Database Schema.

Key Features

Secure Authentication

Supabase Auth (email/password, OAuth)

Two user roles: read-only clerks and editors/admins

Medication Database

~500 medications grouped by drug class

Fields: normal dose, max dose, monitoring parameters & frequency, indications, review frequency

High-risk flag to highlight extra-monitoring drugs

Search & Navigation

Global search by drug name or class

Sidebar displays class summary, indications, monitoring, review schedule for selected drug

Guidelines Sync

Weekly scheduled import (cron) of NICE and SPS monitoring guidance

Subscription & Billing

Practice-wide subscription via Stripe Checkout links

Tiered pricing by practice size

Admin Dashboard

CRUD operations on drugs, classes, and guideline versions

Onboarding tutorial and guided walkthrough for new users

Non-Functional

Performance: search latency <300 ms

Availability: 99.9% uptime

Security: GDPR compliance, encryption at rest/in transit

Scalability: support 500 daily active users, scale to 2,000 concurrent

Technical Stack

Component

Choice

Pros

Cons

Backend & Database

Supabase (PostgreSQL + Edge Functions)

• Single integrated stack

• SQL relational model

• Built-in Auth & Realtime

• Smaller community

• Cold start edge functions

Authentication

Supabase Auth

• Easy integration

• Role-based ACL in DB

• Fewer third-party tutorials

Payment Processing

Stripe Checkout

• Hosted checkout pages

• Webhook support for billing events

• External dependency on Stripe

Guideline Sync

Supabase Cron + Edge Function

• Serverless scheduled jobs

• Direct DB writes

• Limited scheduling flexibility

Hosting & CDN

Vercel / Netlify

• Fast global CDN

• Git integration

• Vendor lock-in risk

Frontend

React + Tailwind CSS

• Component-driven UI

• Rapid styling

• Learning curve for Tailwind purists

Monitoring & Logging

Supabase Logs / Sentry

• Integrated logs

• Error tracking

• May need external dashboard setup

Steps to Implementation

Project Setup

Initialize Supabase project

Scaffold React + Tailwind frontend

Configure Vercel/Netlify for hosting

Authentication & Authorization

Enable Supabase Auth providers

Define users table with roles

Implement sign-up, login, and role-based route guards

Database Schema & Admin UI

Create tables: drug_classes, drugs, drug_indications, drug_monitoring, guideline_versions

Build Admin CRUD UI for classes, drugs, and guideline records

Search & Detail Views

Implement full-text search on drugs.name and drug_classes.name

Create detail page with sidebar showing class summary, indications, monitoring, review schedules, and high-risk banner

Guidelines Sync Pipeline

Develop Edge Function to fetch NICE/SPS data

Schedule weekly cron job

Insert/update guideline_versions and link to drugs

Subscription & Billing

Integrate Stripe Checkout links for practice-wide plans

Handle webhooks to update subscription status in users or practices table

Onboarding & Tutorial

Embed guided walkthrough (e.g., using react-joyride) for first-time users

Testing & Verification

Unit tests for edge functions and React components

End-to-end tests for search, detail view, and payment flow

Load testing to validate <300 ms search latency and concurrency targets

Deployment & Monitoring

Set up CI/CD pipelines

Configure error monitoring (Sentry) and logging

Establish a feedback loop with pilot users

Database Schema

-- Users & Tenancy
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  role TEXT CHECK (role IN ('clerk', 'admin')) NOT NULL,
  practice_id UUID REFERENCES practices(id),
  created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE practices (
  id UUID PRIMARY KEY,
  name TEXT NOT NULL,
  stripe_subscription_id TEXT,
  tier TEXT,
  created_at TIMESTAMP DEFAULT now()
);

-- Drug Classification
CREATE TABLE drug_classes (
  id UUID PRIMARY KEY,
  name TEXT UNIQUE NOT NULL,
  description TEXT
);

-- Medications
CREATE TABLE drugs (
  id UUID PRIMARY KEY,
  name TEXT UNIQUE NOT NULL,
  class_id UUID REFERENCES drug_classes(id),
  normal_dose TEXT,
  max_dose TEXT,
  high_risk_flag BOOLEAN DEFAULT FALSE
);

-- Indications & Review Frequency
CREATE TABLE drug_indications (
  id UUID PRIMARY KEY,
  drug_id UUID REFERENCES drugs(id),
  indication TEXT NOT NULL,
  review_frequency TEXT
);

-- Monitoring Parameters
CREATE TABLE drug_monitoring (
  id UUID PRIMARY KEY,
  drug_id UUID REFERENCES drugs(id),
  parameter TEXT NOT NULL,
  frequency TEXT
);

-- Guideline Versioning
CREATE TABLE guideline_versions (
  id UUID PRIMARY KEY,
  source TEXT CHECK (source IN ('NICE', 'SPS')),
  version TEXT,
  effective_date DATE,
  imported_at TIMESTAMP DEFAULT now()
);

-- Link drugs to guideline versions
CREATE TABLE drug_guidelines (
  drug_id UUID REFERENCES drugs(id),
  guideline_id UUID REFERENCES guideline_versions(id),
  PRIMARY KEY (drug_id, guideline_id)
);

