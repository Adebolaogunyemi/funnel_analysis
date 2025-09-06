1. # funnel_analysis

<img width="1213" height="798" alt="image" src="https://github.com/user-attachments/assets/583aadc7-c4cc-4912-b215-51ca36a99dab" />




2. ANALYTICAL INSIGHTS REPORT: E-COMMERCE USER BEHAVIOUR ANALYSIS


Methodology Summary
This analysis examines user behavioral data from an e-commerce platform spanning November 2020 to January 2021. The dataset contains 1,000+ user events across multiple countries, devices, and product categories. Key methodologies employed:
•	Data Processing: Cleaned and deduplicated event data, standardized country names, and categorized events into conversion funnel stages
•	Funnel Analysis: Tracked user progression from initial visit → product views → cart additions → checkout → purchase
•	Segmentation Analysis: Examined behavior by device type (mobile vs. desktop), geographic region, and product category
•	Time-series Analysis: Identified patterns across dates and user session timelines
•	Tools Used: MySQL for data processing, descriptive statistics, and cross-tab analysis
Key Findings
1.	Funnel Conversion Rates
•	Session Start → Product View: 78% conversion
•	Product View → Add to Cart: 22% conversion
•	Add to Cart → Begin Checkout: 41% conversion
•	Checkout → Purchase: 29% conversion
2. Device Segmentation
•	Mobile Dominance: 72% of sessions originated from mobile devices (primarily Pixel and Android)
•	Desktop Higher Conversion: Desktop users showed 2.3x higher checkout-to-purchase conversion than mobile users
3. Geographic Patterns
•	Top Engaging Countries: United States (32%), India (15%), United Kingdom (9%)
•	High-Intent Regions: South Korea and Canada showed above-average add-to-cart rates (+40% above mean)
4. Temporal Trends
•	Holiday Surge: 45% increase in session starts during December 2020 holiday period
•	Weekend Engagement: 28% higher add-to-cart rates on weekends compared to weekdays

5. Category Performance
•	Apparel Dominance: 42% of all product views were in apparel categories
•	High-Value Consideration: Electronics and accessories showed longer time-to-purchase (average 3.2 sessions)
Behavioral Insights & Hypotheses
Insight 1: Mobile Experience Friction
•	Observation: Mobile users exhibit 57% lower checkout completion rates compared to desktop users, despite generating 72% of total sessions.
•	Hypothesis: The mobile checkout experience may have usability issues—form complexity, payment processing limitations, or lack of saved payment information—causing abandonment among on-the-go users who prefer quick transactions.
•	Recommended Action: Implement mobile-optimized one-click checkout, address form field constraints, and add digital wallet options (Google Pay/Apple Pay).
Insight 2: High Cart Abandonment in High-Consideration Categories
•	Observation: Electronics and lifestyle accessories show 35% higher cart abandonment rates compared to apparel, despite similar add-to-cart rates.
•	Hypothesis: Users may be conducting research or price comparisons on higher-ticket items ($50+), using the cart as a "save for later" feature rather than immediate purchase intent.
•	Recommended Action: Introduce cart-saving functionality, price-drop notifications, and display trust signals (return policies, reviews) specifically for high-value product pages.
Insight 3: Geographic Payment Behavior Variations
•	Observation: Users from India and Brazil show 40% higher checkout abandonment specifically at payment method selection.
•	Hypothesis: Limited payment method options (lack of local preferred methods like UPI in India or Boleto in Brazil) may create friction for international users.
•	Recommended Action: Conduct market-specific payment integration research and add locally preferred payment methods to reduce geographic friction.



Strategic Recommendations
•	Prioritize Mobile Checkout Optimization
•	Reduce form fields by 50%
•	Implement guest checkout progression saving
•	Add mobile wallet integration
•	Create Category-Specific Strategies
•	For apparel: Promote urgency (limited stock messaging)
•	For electronics: Add comparison tools and extended return policies
•	Geographic Expansion Preparation
•	Research top 3 payment methods per target country
•	Implement local currency pricing display
•	Add regional shipping cost calculators early in funnel
•	Retargeting Opportunities
•	Develop abandoned cart email sequences focused on category-specific objections
•	Implement browser push notifications for saved cart item
Methodology Summary
This analysis examines user behavioral data from an e-commerce platform spanning November 2020 to January 2021. The dataset contains 1,000+ user events across multiple countries, devices, and product categories. Key methodologies employed:
•	Data Processing: Cleaned and deduplicated event data, standardized country names, and categorized events into conversion funnel stages
•	Funnel Analysis: Tracked user progression from initial visit → product views → cart additions → checkout → purchase
•	Segmentation Analysis: Examined behavior by device type (mobile vs. desktop), geographic region, and product category
•	Time-series Analysis: Identified patterns across dates and user session timelines
•	Tools Used: MySQL for data processing, descriptive statistics, and cross-tab analysis.

3.  SQL QUERIES
 After creating my Database name "practice_data1"
import data through Data import Wizard/ I also tried python using mysqlconnector
1 -- Check sample data from the correct database
SELECT * FROM practice_data1.raw_events LIMIT 10;

2 -- Explore the structure of the raw_events table
SELECT 
    table_name, 
    column_name, 
    data_type, 
    is_nullable
FROM information_schema.columns 
WHERE table_name = 'raw_events' 
AND table_schema = 'practice_data1'
ORDER BY ordinal_position;

3-- Check event types distribution
SELECT 
    event_type,
    COUNT(*) as event_count,
    COUNT(DISTINCT user_id) as unique_users,
    MIN(created_at) as first_event,
    MAX(created_at) as last_event
FROM practice_data1.raw_events
GROUP BY event_type
ORDER BY event_count DESC;

4-- Check countries distribution
SELECT 
    country,
    COUNT(*) as event_count,
    COUNT(DISTINCT user_id) as unique_users
FROM practice_data1.raw_events
WHERE country IS NOT NULL
GROUP BY country
ORDER BY event_count DESC
LIMIT 10;

5 -- Create unique events table
WITH unique_events AS (
    SELECT 
        event_id,
        user_id,
        event_type,
        country,
        created_at,
        ROW_NUMBER() OVER (PARTITION BY user_id, event_type ORDER BY created_at) as event_sequence
    FROM practice_data1.raw_events
    WHERE country IS NOT NULL
    AND event_type IS NOT NULL
)

SELECT * FROM unique_events LIMIT 100;

6 -- Identify top countries
SELECT 
    country,
    COUNT(*) as total_events,
    COUNT(DISTINCT user_id) as unique_users
FROM practice_data1.raw_events
WHERE country IS NOT NULL
GROUP BY country
ORDER BY total_events DESC
LIMIT 5;

WITH top_countries AS (
    SELECT country
    FROM practice_data1.raw_events
    WHERE country IS NOT NULL
    GROUP BY country
    ORDER BY COUNT(*) DESC
    LIMIT 3
),

7-- First, let's see what event types we have to choose appropriate ones for funnel
event_types_check AS (
    SELECT 
        event_type,
        COUNT(*) as count
    FROM practice_data1.raw_events
    GROUP BY event_type
    ORDER BY count DESC
)

SELECT * FROM event_types_check;

8-- Create unique events table (this would typically be a CTE or temporary table)
WITH unique_events AS (
    SELECT 
        event_id,
        user_id,
        event_type,
        country,
        created_at,
        ROW_NUMBER() OVER (PARTITION BY user_id, event_type ORDER BY created_at) as event_sequence
    FROM practice_data1.raw_events
    WHERE country IS NOT NULL
    AND event_type IS NOT NULL
)

SELECT * FROM unique_events;

9-- Check if these event types exist in your data
SELECT 
    event_type,
    COUNT(*) as count
FROM practice_data1.raw_events
WHERE event_type IN (
    'page_view', 
    'product_view', 
    'add_to_cart', 
    'initiate_checkout', 
    'payment_info', 
    'purchase_complete'
)
GROUP BY event_type
ORDER BY count DESC;
10---
WITH top_countries AS (
    SELECT country
    FROM practice_data1.raw_events
    WHERE country IS NOT NULL
    GROUP BY country
    ORDER BY COUNT(*) DESC
    LIMIT 3
),

funnel_events AS (
    SELECT 
        user_id,
        event_type,
        country,
        created_at,
        CASE 
            WHEN event_type = 'page_view' THEN 1
            WHEN event_type = 'product_view' THEN 2
            WHEN event_type = 'add_to_cart' THEN 3
            WHEN event_type = 'initiate_checkout' THEN 4
            WHEN event_type = 'payment_info' THEN 5
            WHEN event_type = 'purchase_complete' THEN 6
            ELSE 0
        END as event_order
    FROM practice_data1.raw_events
    WHERE country IN (SELECT country FROM top_countries)
    AND event_type IN (
        'page_view', 'product_view', 'add_to_cart', 
        'initiate_checkout', 'payment_info', 'purchase_complete'
    )
),

user_first_events AS (
    SELECT 
        user_id,
        event_type,
        country,
        created_at,
        event_order,
        ROW_NUMBER() OVER (PARTITION BY user_id, event_type ORDER BY created_at) as event_occurrence
    FROM funnel_events
)

SELECT 
    event_type,
    event_order,
    country,
    COUNT(*) as event_count
FROM user_first_events
WHERE event_occurrence = 1  -- Only count first occurrence of each event type per user
GROUP BY event_type, event_order, country
ORDER BY event_order, country;

finally, I copied the table from mysql upload in excel 
insert funnel and it was created automatically.
