# Semester Kickoff AI Workshop

### Use the Following Instructions to Complete the AI Workshop:
1. Login to GCP with the following credentials:
   - **Username:** UVU Email
   - **Password:** dsclub2023!
3. If necessary, select the ***"ds-club-master-project"*** Project
4. Go to ***BigQuery***
5. On the three dots, click **"Create Dataset"**
6. In the ***Dataset ID*** field, type **"[first_name]_[last_name]_ncaa"**
7. In the ***Location Type*** field, select ***Multi-region***, and then select US (multiple regions in the United States)
8. Click ***Create Dataset***
9. In the ***Explorer*** Pane, select ***+Add***
10. Under ***Additional Sources***, select ***"Star a project by name"***
11. In the dialog box, type ***"bigquery-public data"***. Click ***"Star"***
12. Once you have access to the ***"bigquery-public-data"*** project, search ***"ncaa"*** in the search bar in the ***Explorer*** Pane
13. With the ***ncaa_basketball*** dataset present, click the arrow to view all the tables in the dataset
14. Click the three dots to the right of the ***"mbb_historical_tournament_games"*** table. Select ***"Open"***
15. Read the column descriptions in the ***Schema*** tab to get an idea of the data we're working with.
16. Open the ***Query*** dropdown menu and select ***"In new tab"***
17. Write the following query:
```
SELECT
    season,
    COUNT(*) as games_per_tournament
FROM
`bigquery-public-data.ncaa_basketball.mbb_historical_tournament_games`
GROUP BY
    season
ORDER BY
    season;
19. Run the query
20. Return to the "mbb_historical_tournament_games" table view.
21. Go to the Preview tab to get a sample of the table records
22. Open a new query window and run the following query:
# create a row for the winning team
SELECT
# features
    season,
    round,
    days_from_epoch,
    game_date,
    day,
    'win' AS label,
    win_seed AS seed,
    win_market AS market,
    win_name AS name,
    win_alias AS alias,
    win_school_ncaa AS school_ncaa,
    # win_pts AS points,
    lose_seed AS opponent_seed,
    lose_market AS opponent_market,
    lose_name AS opponent_name,
    lose_alias AS opponent_alias,
    lose_school_ncaa AS opponent_school_ncaa
    # lose_pts AS opponent_points
FROM
`bigquery-public-data.ncaa_basketball.mbb_historical_tournament_games`
UNION ALL
# create a separate row for the losing team
SELECT
# features
    season,
    round,
    days_from_epoch,
    game_date,
    day,
    'loss' AS label,
    lose_seed AS seed,
    lose_market AS market,
    lose_name AS name,
    lose_alias AS alias,
    lose_school_ncaa AS school_ncaa,
    # lose_pts AS points,
    win_seed AS opponent_seed,
    win_market AS opponent_market,
    win_name AS opponent_name,
    win_alias AS opponent_alias,
    win_school_ncaa AS opponent_school_ncaa,
    # win_pts AS opponent_points
FROM
`bigquery-public-data.ncaa_basketball.mbb_historical_tournament_games`
```
22. Keep that query tab open and open another query tab.
23. In the new query tab, run the following query (replace the strings in brackets with the name of your dataset):
```
CREATE OR REPLACE MODEL
    `[first_name]_[last_name]_ncaa.ncaa_model`
OPTIONS
    (model_type='logistic_reg') AS
# create a row for the winning team
SELECT
# features
    season,
    'win' AS label,
    win_seed AS seed,
    win_school_ncaa AS school_ncaa,
    lose_seed AS opponent_seed,
    lose_school_ncaa AS opponent_school_ncaa,
FROM
`bigquery-public-data.ncaa_basketball.mbb_historical_tournament_games`
WHERE
    season <= 2017
UNION ALL
# create a separate row for the losing team
SELECT
# features
    season,
    'loss' AS label,
    lose_seed AS seed,
    lose_school_ncaa AS school_ncaa,
    win_seed AS opponent_seed,
    win_school_ncaa AS opponent_seed_ncaa
FROM
`bigquery-public-data.ncaa_basketball.mbb_historical_tournament_games`

# now we split our dataset with a WHERE clause so we can train on a subset of data and then evaluate and test the model's performance against a reserved subset so the model doesn't memorize or overfit to the training data.

WHERE season <= 2017
```
24. Wait 3-5 minutes for your model to be created. Once created, expand the dataset you created at the beginning of the workshop, expand the ***Models*** folder and select your model.
25. Go to the ***Evaluation*** tab to view your model's performance.
