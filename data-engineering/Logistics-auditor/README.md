## A. Executive Summary

This audit analyzed Veridi Logistics' delivery performance by combining order, review, and customer location data. On average, 89% of orders were delivered on time, but late deliveries are heavily concentrated in specific regions rather than spread nationwide: Alagoas (AL) and Maranhão (MA) had the highest late rates, at roughly 24% and 20%, compared to under 6% in São Paulo. Late deliveries strongly hurt customer satisfaction, with average review scores dropping from 4.3 (On Time) to 1.8 (Super Late). Product category also plays a role: categories like audio, fashion/underwear, and electronics show above-average late rates, suggesting the problem is driven by both regional logistics and specific product handling, not delivery speed alone.

## B. Project Links

- Link to Notebook: https://colab.research.google.com/drive/1zimkIr52FmvLeZj7BL6BkN7nESm2VkF1?usp=sharing
- Link to Dashboard: https://datastudio.google.com/reporting/02e92443-ef7b-4e12-8c39-9fafac65cc59
- Link to Presentation: https://docs.google.com/presentation/d/1kG-t-d-I_Acr2xlLcRAwYopPbqbb1c06nbaJX8QGDow/edit?usp=sharing

## C. Technical Explanation

**Data Cleaning:** I joined the orders, reviews, customers, and order_items tables on their respective keys. Reviews and order items both had a 1-to-many relationship with orders (some orders had multiple reviews or items), which initially caused duplicate rows after merging. I fixed this by keeping only the most recent review and the first item per order, bringing the row count back to the original 99,441 orders. Orders with no delivery date (canceled/unavailable) were kept but flagged as "Not Delivered" rather than excluded, so they're visible but don't distort the delay calculations.

**Candidate's Choice:** I added a "Late Delivery Rate by Product Category" analysis. The original brief focused only on geography, but I wanted to check whether certain product types are inherently harder to deliver on time (e.g., due to fragility, specialized sellers, or packaging needs). This showed that categories like audio and electronics have above-average late rates, giving Veridi a second lever (product-specific carrier/packaging improvements) alongside regional fixes.

---
# Project Brief: The "Last Mile" Logistics Auditor

**Client:** Veridi Logistics (Global E-Commerce Aggregator)
**Deliverable:** Public Dashboard, Code Notebook & Insight Presentation

---

## 1. Business Context

**Veridi Logistics** manages shipping for thousands of online sellers. Recently, the CEO has noticed a spike in negative customer reviews. She has a "gut feeling" that the problem isn't just that packages are late, but that the estimated delivery dates provided to customers are wildly inaccurate (i.e., we are over-promising and under-delivering).

She needs you to audit the delivery data to find the root cause. She specifically wants to know: **"Are we failing specific regions, or is this a nationwide problem?"**

Your job is to build a "Delivery Performance" audit tool that connects the dots between **Logistics Data** (when a package arrived) and **Customer Sentiment** (how they rated the experience).

## 2. The Data

You will use the **Olist E-Commerce Dataset**, a real commercial dataset from a Brazilian marketplace. This is a relational database dump, meaning the data is split across multiple CSV files.

- **Source:** [Kaggle - Olist Brazilian E-Commerce Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
- **Key Files to Use:**
  - `olist_orders_dataset.csv` (The central table)
  - `olist_order_reviews_dataset.csv` (Sentiment)
  - `olist_customers_dataset.csv` (Location)
  - `olist_products_dataset.csv` (Categories)

## 3. Tooling Requirements

You have the flexibility to choose your development environment:

- **Option A (Recommended):** Use a cloud-hosted notebook like **Google Colab**, or **Deepnote**, etc.
- **Option B:** Use a local **Jupyter Notebook** or **VS Code**.
  - _Condition:_ If you choose this, you must ensure your code is reproducible. Do not reference local file paths (e.g., `C:/Downloads/...`). Assume the dataset is in the same folder as your notebook.
- **Dashboarding:** The final output must be a **publicly accessible link** (e.g., Tableau Public, Google Looker Studio, Streamlit Cloud, or PowerBI Web, etc.).

---

## 4. User Stories & Acceptance Criteria

### Story 1: The Schema Builder

**As a** Data Engineer,
**I want** to join the Orders, Reviews, and Customers tables into a single master dataset,
**So that** I can analyze a customer's location and their review score in the same row.

- **Acceptance Criteria:**
  - Load the raw CSVs into your notebook.
  - Perform the correct joins (e.g., join Reviews to Orders on `order_id`, join Customers to Orders on `customer_id`).
  - **Check:** Ensure you don't accidentally duplicate rows (a common error with 1-to-many joins).

### Story 2: The "Real" Delay Calculator

**As a** Logistics Manager,
**I want** to know the difference between the "Estimated Delivery Date" and the "Actual Delivery Date,"
**So that** I can see how often we are lying to customers.

- **Acceptance Criteria:**
  - Create a new calculated column: `Days_Difference` = `order_estimated_delivery_date` - `order_delivered_customer_date`.
  - Classify orders into statuses: "On Time", "Late", and "Super Late" (> 5 days late).
  - Handle missing values: Some orders were never delivered (`order_status` = 'canceled' or 'unavailable'). These should be excluded or flagged separately.

### Story 3: The Geographic Heatmap

**As a** Regional Director,
**I want** to see which specific States (`customer_state`) have the highest percentage of late deliveries,
**So that** I can focus my repair efforts on the worst regions.

- **Acceptance Criteria:**
  - Calculate the % of late orders per State.
  - Visualize this on a map or a bar chart.
  - **Insight:** Identify if "Remote" states (far from the distribution center) are disproportionately affected.

### Story 4: The Sentiment Correlation

**As a** Customer Success Lead,
**I want** to see if late deliveries actually cause bad reviews,
**So that** I can prove to the CEO that logistics is the problem.

- **Acceptance Criteria:**
  - Create a visualization comparing "Delivery Delay (Days)" vs "Average Review Score (1-5)".
  - Show the average review score for "On Time" orders vs. "Late" orders.

---

## 5. Bonus User Story: The "Translation" Challenge

**As a** Global Analyst,
**I want** to see product categories in **English**, not Portuguese,
**So that** I can understand if "Furniture" is harder to ship than "Electronics".

- **Acceptance Criteria:**
  - The `product_category_name` is in Portuguese (e.g., `cama_mesa_banho`).
  - Use the `product_category_name_translation.csv` file included in the dataset (or create your own mapping) to translate these into English for your final dashboard.

---

## 6. The "Candidate's Choice" Challenge

**As a** Creative Problem Solver,
**I want** to include one extra feature or analysis that adds specific business value,
**So that** I can demonstrate my ability to think beyond the basic requirements.

- **Instructions:**
  - Add one more metric, chart, or drill-down.
  - **Requirement:** You must justify _why_ this feature matters to the business in your README.

---

## 7. Submission Guidelines

Please edit this `README.md` file in your forked repository to include the following three sections at the top:

### A. The Executive Summary

- A 3-5 sentence summary of your findings.

### B. Project Links

- **Link to Notebook:** (e.g., Google Colab, etc.). _Ensure sharing permissions are set to "Anyone with the link can view"._
- **Link to Dashboard:** (e.g., Tableau Public, etc.).
- **Link to Presentation:** A link to a short slide deck (PDF/PPT) AND (Optional) a 2-minute video walkthrough (YouTube) explaining your results.

### C. Technical Explanation

- Briefly explain how you handled the "Data Cleaning".
- Explain your "Candidate's Choice" addition.

**Important Note on Code Submission:**

- Upload your `.ipynb` notebook file to the repo.
- **Crucial:** Also upload an **HTML or PDF export** of your notebook so we can see your charts even if GitHub fails to render the notebook code.
- Once you are ready, please fill out the [Official Submission Form Here](https://forms.cloud.microsoft/e/CeQN2mCyUr) with your links

---

## 🛑 CRITICAL: Pre-Submission Checklist

**Before you submit your form, you MUST complete this checklist.**

> ⚠️ **WARNING:** If you miss any of these items, your submission will be flagged as "Incomplete" and you will **NOT** be invited to an interview.
>
> **We do not accept "permission error" excuses. Test your links in Incognito Mode.**

### 1. Repository & Code Checks

- [ ] **My GitHub Repo is Public.** (Open the link in a Private/Incognito window to verify).
- [ ] **I have uploaded the `.ipynb` notebook file.**
- [ ] **I have ALSO uploaded an HTML or PDF export** of the notebook.
- [ ] **I have NOT uploaded the massive raw dataset.** (Use `.gitignore` or just don't commit the CSV).
- [ ] **My code uses Relative Paths.**

### 2. Deliverable Checks

- [ ] **My Dashboard link is publicly accessible.** (No login required).
- [ ] **My Presentation link is publicly accessible.** (Permissions set to "Anyone with the link can view").
- [ ] **I have updated this `README.md` file** with my Executive Summary and technical notes.

### 3. Completeness

- [ ] I have completed **User Stories 1-4**.
- [ ] I have completed the **"Candidate's Choice"** challenge and explained it in the README.

**✅ Only when you have checked every box above, proceed to the submission form.**

---
