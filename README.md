# Events Synthetic Consumer Research

> Using AI-generated synthetic consumers to optimize event design through conjoint analysis 

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)

## Overview

This project applies discrete choice modeling (conjoint analysis) to event design optimization using **GPT-3.5-generated synthetic consumers** as respondents. The research addresses two key questions for event organizers:

1. **Free Event Sign-Up:** Which features drive students to participate in free hackathons?
2. **Premium Add-On Pricing:** How much would students pay for premium services (mentoring, workshops, exclusive events)?

By simulating CS student decision-making across 100+ randomized choice scenarios, this methodology enables rapid iteration on event design without expensive participant recruitment.

## Motivation

Traditional conjoint studies require recruiting hundreds of real participants, which is:
- **Time-intensive** (weeks of recruitment)
- **Expensive** ($15-50 per participant)
- **Logistically complex** (IRB, scheduling, quality control)

This project demonstrates an alternative: **synthetic consumers powered by large language models**. GPT-3.5 simulates student preferences based on its training data, allowing researchers to:
- Test dozens of event configurations in hours
- Iterate on experimental design cheaply
- Generate directional insights before investing in real participant studies

## Research Questions

### Study 1: Free Event Participation Drivers
**Question:** What hackathon features increase sign-up likelihood?

**Tested Attributes:**
- Rewards & Resources (cash prizes, API credits, recognition)
- Peer Quality & Prestige (MIT/Stanford vs. local hubs)
- Challenge Structure (themed tracks vs. open innovation)
- Sprint Format (24-hour vs. multi-day vs. async)
- Venture Track (accelerator programs, demo days)
- Network & Career Access (guaranteed interviews, 1:1 mentoring)

### Study 2: Willingness to Pay for Premium Add-Ons
**Question:** How should we price premium features?

**Tested Add-Ons:**
- On-Demand Learning Platform
- Live Classes & Workshops
- 1:1 Mentoring Sessions
- Exclusive VIP Events
- **Price Range:** $0 - $49

## Methodology

### Conjoint Analysis with Synthetic Respondents

**Experimental Design:**
- **100 choice tasks** per study (A vs B comparisons)
- **5 synthetic respondents** per task (GPT-3.5-turbo)
- **Regularized logistic regression** (ridge penalty)
- **Total observations:** ~1,000 per study

### GPT-3.5 Prompt Engineering

Each synthetic respondent was given:
```
System: "You're a pragmatic CS student evaluating events. 
Compare the options and pick the one that offers better value 
for your time and career."

User: [Two event profiles with randomized features]
```

**Temperature = 0.6** balances:
- **Determinism** (low temp → consistent logic)
- **Stochasticity** (high temp → individual variation)

At 0.6, synthetic respondents show:
- Consistent preference patterns (same person wouldn't flip choices randomly)
- Realistic heterogeneity (different "students" have different priorities)
- Stable estimates across runs (±5% variation)

**Why not lower?** At temp=0.2, all respondents behave identically.  
**Why not higher?** At temp=0.9, choices become too random to extract stable preferences.

### Analysis Pipeline

1. **Profile Generation:** Create balanced choice sets (70% weak features, 30% strong)
2. **Synthetic Data Collection:** Query GPT-3.5 for 500-1000 choices
3. **Long Format Conversion:** Expand to alternative-specific dataset
4. **Model Estimation:** Ridge-regularized multinomial logit
5. **WTP Calculation:** Utility coefficients ÷ price sensitivity

## Project Structure
```
Events-Synthetic-Consumer-Research/
├── notebooks/
│   ├── 1_free_event_data_collection.ipynb
│   ├── 2_free_event_analysis.ipynb
│   ├── 3_paid_addon_data_collection.ipynb
│   └── 4_paid_addon_analysis.ipynb
├── data/
│   ├── free_event_conjoint_long.csv
│   ├── free_event_utilities.csv
│   ├── paid_addon_conjoint_long.csv
│   ├── paid_addon_utilities.csv
│   └── paid_addon_wtp.csv
├── README.md
├── requirements.txt
└── .gitignore
```

## Setup & Installation

### Prerequisites
- Python 3.8+
- OpenAI API key ([get one here](https://platform.openai.com/api-keys))

### Installation
```bash
# Clone repository
git clone https://github.com/antonhantel/Events-Synthetic-Consumer-Research.git
cd Events-Synthetic-Consumer-Research

# Install dependencies
pip install -r requirements.txt

# Set API key
export OPENAI_API_KEY="YOUR_OPENAI_API_KEY"
```

### Running the Analysis

Execute notebooks in sequence:
```bash
jupyter notebook notebooks/1_free_event_data_collection.ipynb
# Wait for data collection (~5-10 minutes)

jupyter notebook notebooks/2_free_event_analysis.ipynb
# Review results

# Repeat for paid add-on study (notebooks 3-4)
```

## Technical Details

### Model Specifications

**Free Event Study:**
- Regularization: α = 0.3 (ridge)
- Pseudo R²: 0.54
- Features: 16 dummy variables

**Paid Add-On Study:**
- Regularization: α = 0.02 (weaker penalty to preserve price slope)
- Price coefficient: -0.08 (negative as expected)
- WTP = Utility / |Price Coefficient|
