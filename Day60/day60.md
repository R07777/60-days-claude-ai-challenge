# Day 60 — Final Capstone: Review, Portfolio & Graduation

## Overview

Day 60 marks the final practical milestone of the AB Talks 60-Day Claude AI Challenge.

For the final milestone, I worked on a complete 10-day software development capstone focused on taking an AI-powered application from requirements and implementation through testing, deployment, documentation, portfolio preparation, and production release.

## Capstone Project

**Project:** DebugMate

DebugMate is an AI-powered debugging assistant that helps developers diagnose programming errors, understand what went wrong, and maintain a history of their debugging sessions.

## Technology Stack

- React
- Vite
- Node.js
- Express.js
- MongoDB
- MongoDB Atlas
- Gemini API
- JWT Authentication
- Netlify
- Render
- GitHub

## What I Practiced

Throughout the capstone, I applied skills developed during the 60-Day Claude AI Challenge, including:

- AI-assisted software development
- Prompt engineering
- Product thinking
- Requirements analysis
- Full-stack development
- API development
- Authentication and authorization
- Database integration
- AI API integration
- Error handling
- Security practices
- Debugging
- Git and GitHub workflows
- Feature branches and pull requests
- Production deployment
- Documentation
- Product review
- Portfolio preparation

## Production Deployment

### Frontend

https://debugmate-ai.netlify.app

### Backend

https://debugmate-api.onrender.com

### Source Code

https://github.com/R07777/debugmate

## Deployment Challenges Solved

During the production deployment process, I identified and fixed real deployment issues rather than only testing locally.

### 1. Frontend API configuration

The frontend environment variable initially required correction so that API requests pointed to the correct production API path.

### 2. Netlify SPA routing

Refreshing a React route such as `/history` directly initially produced Netlify's 404 page.

I fixed this by adding:

`client/public/_redirects`

with:

```text
/*    /index.html   200
