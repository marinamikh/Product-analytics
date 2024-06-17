Hello!
Link to my project : https://docs.google.com/spreadsheets/d/1azNvkD6Zgcrf5r_LWW6veSX8-u8LQrGlUT2hu5O1m4s/edit?usp=sharing

Main query I used for the task:

      WITH user_eng AS (SELECT user_pseudo_id AS userID, event_name,
                         MIN(TIMESTAMP_MICROS(event_timestamp)) AS first_event_date
                  FROM `turing_data_analytics.raw_events`
                  WHERE event_name = 'session_start'
                  GROUP BY 1,2
                  ORDER BY 1),
            purchase AS (SELECT user_pseudo_id AS userID, 
                        MIN(TIMESTAMP_MICROS(event_timestamp)) AS purchase_date
                  FROM `turing_data_analytics.raw_events`
                  WHERE event_name = 'purchase'
                  GROUP BY 1)
     SELECT u.userID, u.first_event_date, p.purchase_date,DATE(u.first_event_date) AS date, u.event_name,
        TIMESTAMP_DIFF(p.purchase_date,u.first_event_date, MINUTE) as difference_min,
        TIMESTAMP_DIFF(p.purchase_date,u.first_event_date, SECOND) as difference_sec
     FROM user_eng u
     JOIN purchase p
     ON u.userID=p.userID AND DATE(u.first_event_date) = DATE(p.purchase_date)
     WHERE  u.first_event_date < p.purchase_date
     GROUP BY 3,1,2,4,5,6
     ORDER BY 4
All my insights you'll also find in that GoogleSheets document.
