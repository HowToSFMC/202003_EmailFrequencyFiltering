
Filtering audiences on frequency and recency:

  

There are assumptions made around a data model for this process.

  

These are:

-   There is a primary master data extension used for audience selections
    
-   You have Contact Builder with custom attribute groups or are able to implement those
    
-   You are happy looking back no more than 6 months
    

  

A standard model that I’ve used for monitoring and managing is with a related engagement data extension, connected in Contact Builder as well as in a 1 to 1 data relationship within Email Studio.

  

There would be a data extension per monitored engagement stat, eg: Sends, Opens, Clicks (+ Uniques if required) with a consistent set of time periods.

  

Eg:

  

![](https://lh3.googleusercontent.com/qxIg8-R9CHSSFISpYsHct4E_B5v7-hO1KtHKpzjiGRLuUIkEY9MZ5mcLl7_lpTCP7rVL-32arRZxGwTCvDjYg4rQZEnGQmE1jQH7oirCNP1A6nQMqGJxWeBAQnHIm82P1PyoZVhF)

  

Then an Opens and Clicks version of the same Data Extension. These get joined to create a master engagement DE, with SQL Queries running at the defined frequency. Commonly 1 day is sufficient. However, if the business model relies on multiple sends per day and is required, the SQL query can be tweaked to include Send_2H for example. (Sample query below)

  

---

  

select

s.subscriberkey

,SUM(CASE When s.EventDate > getdate()-7 Then 1 Else 0 End ) as 'Send_7D'

,SUM(CASE When s.EventDate > getdate()-10 Then 1 Else 0 End ) as 'Send_10D'

,SUM(CASE When s.EventDate > getdate()-14 Then 1 Else 0 End ) as 'Send_14D'

,SUM(CASE When s.EventDate > getdate()-28 Then 1 Else 0 End ) as 'Send_28D'

,SUM(CASE When s.EventDate > getdate()-60 Then 1 Else 0 End ) as 'Send_60D'

,SUM(CASE When s.EventDate > getdate()-90 Then 1 Else 0 End ) as 'Send_90D'

,SUM(CASE When s.EventDate > getdate()-180 Then 1 Else 0 End ) as 'Send_180D'

from

_Sent s

group by s.subscriberkey

  

---

This will populate the number of sends by 7 days, 10 days, 14 days etc.. based on a business requirement. Change the getdate()-X to get a different number of days looked back. If you need to look back beyond 6 months, you will need a custom method to track event data beyond Data Views.

---

This final joined Data Extension is related to the Master Audience data extension and filtering can occur in Email Studio to match the engagement criteria. It can also be added to an Engagement Attribute group within Contact Builder to support decision splits based on behaviour outside of the journey.

---

Depending on your method of email dispatch, if you are using a batched sending process (User Initiated, Salesforce Send or A/B Test) you are able to create lack-of-engagement exclusion data extensions using filtered DEs for standard engagement exclusions. Eg: a Filtered data Extension for everyone who has been sent 2 emails in the last 7 days, but has not opened an email in 30 days may be a frequency exclusion requirement. These data extensions can automatically refresh as part of the automation that generates the engagement stats.

![](https://lh3.googleusercontent.com/urIymjci8RJJr1RLNMSNkGAhUDWyW8-o6Jald_l7YFO6LDwBje9pq0q2qBvAgV381xMMbjfMhAmvlTg_9a4nC3Ww-opx5dh1rG6oD4_9XEixe3SSG4NffnPfgbTaqxBJ9Xm-RAH0)

If you are using Journey Builder for your dispatches, you have a few options:

-   Use an exclusion script on your email send activities looking at the same filtered DEs
    
-   Create your audience using SQL and the engagement stats in the DE
    
-   Use filtering at the entry step to exclude disengaged people
    

  

---

  

Further considerations:

Different Send Types

If you have different types of send that you are able to identify using Data Views, ie: using different email asset names, joining the engagement stats to the _Job data view to create more granular engagement stats. One example could be if you had a weekly sales email as a type of campaign and a monthly newsletter. You may not want to use the weekly sales email engagement in the segmentation criteria of the monthly newsletter. Adding a filter on the SQL query to populate DealSend_7D against the _Job data view would make that possible.
  

Recipient Domain Splits

Including recipient domains in the filtering related Data Extension to make it easier for managing inbox placement at key domains - eg: all Microsoft, Oath and Comcast get clustered and you can apply different levels of engagement requirements to maximise commercial opportunity against inbox reputation.

  

Additional Engagement Metrics

Tying your email engagement to web activity is a useful tool, if you can identify people who have not engaged with email for a while but are recently active on the website. It provides you an additional data point to create a bespoke audience for a new campaign type.

  

---

  

Using the model is relatively simple once it is deployed. You can create filtered data extensions from your core audience selection DE and have those refresh on an appropriate cadence. It creates a single view of contact engagement to use across all campaigns without necessarily expecting non-technical marketers to write SQL or using external tools to create audiences with engagement considerations.

  

It’s a fairly modular concept, you can add or remove pre-made exclusion Data Extensions to batch sending without significant additional work. You can also apply the exclusion when filtering the audience selection Data Extension if you want a more accurate estimate of dispatch audience prior to send.
