  
1.  Create a Profile attribute on Subscriber level to capture the number of max email sends per day for a subscriber (call it MaxDailyEmailSendCnt) – keep it as hidden as this is for internal tracking only. You can configure it as numeric with max 3 digits and a default value – for e.g. “1” if you wish to restrict this to a single email per subscriber email id daily.
    

![](https://lh5.googleusercontent.com/9TvXPfKAhw-zu7Ic-wJtA7KrBf1b-b4wWJ7jcZfgpXCixTsu9izOpZcygfPX4JVO6kbTgvJSAiv_1akRvQONjMIeBLf-jJoNCpikmrugqNKwsdGD5YMcMi5NFUut7edrMu7q7iA)![](https://lh6.googleusercontent.com/0nYcEfVh957_DUZ2j16Eu1XMsfgzM1105SIyKT8p6TrUJbRgAaRK07anCmRdT2kR3XqKzMGFcd9Tnpi8_6-bB_aSUJoxtLnqaSMwbhc-7uePpRpeh6CIcYnEtW5UgFpkO3XCjA4)

  

2.  Enable the SendLog DE (if not already enabled) – add additional attributes for storing EmailID and SendDate (latter can be defaulted to current date)
    

  

![](https://lh6.googleusercontent.com/k_xJ19csz8E04M0j4FHgn0sRNXhODI8E20b453nSaK5lTxWI1VdUq6RGRQQVgb67S6CPbPQy28fGFltfEJISIReMgP1I_aq5x1Wrz9ZoSUS5_1QLA9v5dYhvx-HiAMfbdZ0jA8c)

  
  

3.  Create a Filter (e.g. SubscribersSentEmailToday) to segment subscribers from the SendLog DE with email id who were sent one or more emails for current day
    

  

![](https://lh5.googleusercontent.com/nipB-5vaPzHB8WmWTNNrAm5ytlCD-GGhJfvubMNZX-RzIM018_zeyqPnZZG0vNAxNkKNPeo9YVHVjsbljOKFzKZJp3lrlfBLxe8OXXb6NY2b_Y9wv61gVtHQHpzpNHG5PNPESTU)

  

4.  Create a Filter Activity in Automation Studio “Refresh Todays Email Sends Info” – you could add this to an Automation workflow and schedule it or run manually to refresh the Filtered Data Extension “TodaysSendDE”. This Filter Activity can be run after every scheduled Email Send or few hours a day depending on the number of unique Email sends expected from the account per day.
    

  

![](https://lh3.googleusercontent.com/rhS2SmzHtxvfzxDT26Odb0FZ2nXKKbQ-7g0mabClhTOiHFO3exrFxuC26BUwf8ZNWI6Mn3eKctgByBoDer9xrPNLDnHGNyGUorl9-9thmVPScc1oMPfVhAMVOrDqWHV4ELv7l_s)

  

![](https://lh3.googleusercontent.com/Y_gVnlEKZky21_O_CCHZFQ0IgBzdQJLQsaWPmRHwwZtNvjk1lTGDOw8rr3rl-D8LdO3a5d7ZNO5ORXv-amALi9bF9b9I1psVJgw-p0C7LVRyUYronwv7-0Sij379twLc-OrS34I)

5.  Use an exclusion script (Ampscript with RowCount and LookUpRows) to check the count of records per email id in the TodaysSendDE - if > than MaxDailyEmailSendCnt for the email id, then script should become true and exclude the subscriber from the Send
    

  

![](https://lh3.googleusercontent.com/KROWzrR0wSNMG6-QMjEm0pll9tCBV6v3ayrjA4cikotS6QIg2J_bs5GRolK69LNExjXycx1fDc0-9ETt8vscAZ1qOBhFA2dYwvJTsrArdUgIVs4klTQ3RE_Kwoxh1tvBLH2P2pc)

  

Additional points to consider

  

1.  If there are multiple subscribers with same email id, then all of them would be considered within the limit as this logic checks by email id and not by subscriber id. If the logic needs to be done by subscriber id, then you can use the “subid” column in the SendLog DE and in the Ampscript LookUpRows function, check with the SubscriberId attribute (Personalization string) instead.
    

  

2.  In order to increase the daily send limit, you can update the MaxDailyEmailSendCnt attribute on the Profile for the Subscribers – to do a mass update, export the data to a csv, update this field and then reimport.
    
3.  An alternate option would be to consider using Query Activity on the Sent and Subscriber Dataviews to get the Email addresses of subscribers sent to - and populating the TodaysSendDE (fields need to be configured to match the query results). The rest of the logic would remain the same.
