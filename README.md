# Maven-Music-Challenge

## About this project

Music has a unique way of shaping our moments and defining experiences. Spotify Wrapped, an annual recap of personal listening habits, has become a cultural phenomenon that allows users to reflect on their favorite artists, tracks, and trends. This project recreates the Spotify Wrapped experience using Power BI, enabling an interactive and detailed exploration of music data.

The objective was to transform raw streaming history data into meaningful insights while applying best practices in data analysis and visualization. By leveraging Power BI and SQL, this project demonstrates the value of storytelling through data, focusing on trends, preferences, and engagement patterns.

## Key Insights

*1. Listening Habits:*
- Identification of most active hours and days for streaming music.
- Analysis of shuffle mode usage and reasons for starting or ending tracks.

*2. Top Artists and Tracks:*
- Dynamic ranking of favorite artists and most-played tracks using DAX measures.
- Percentage breakdown of listening time dedicated to top artists.

*3. Record-Breaking Moments:*
- Highlighting the day with the highest listening time and the tracks that defined it.

*4. Platform Analysis:*
- Insights into the most-used devices and platforms, with percentage contributions to total listening time.

*5. Dynamic Summary:*
- A narrative-driven summary showcasing total listening time, favorite artist, and most-played track.


## Tools and Techniques
*1. SQL:*
- Queried and aggregated raw data for initial exploration and transformations.
- Created pre-aggregated datasets, such as determining the most-played track for each year.
- 
*2. Power BI:*
- Built interactive dashboards and visualizations to present findings in an engaging way.
- Applied DAX measures for dynamic calculations and insights.
- 
*3. DAX Measures:*
  
- **Top Artist:** Identifies the artist with the highest listening time:
```
Top Artist = 
MAXX(
    TOPN(
        1,
        SUMMARIZE(
            'spotify_history',
            'spotify_history'[artist_name],
            "TotalListeningTime", SUM('spotify_history'[listening_time_min])
        ),
        [TotalListeningTime],
        DESC
    ),
    [artist_name]
)
```

- **Platform Usage for Favourite (%):** Calculates the percentage of listening time for the favorite platform:
```
Platform Usage for Favourite (%) = 
VAR FavPlatform = [Favourite Platform]
VAR ListeningTimeForFavPlatform = 
    CALCULATE(
        SUM('spotify_history'[listening_time_min]),
        'spotify_history'[platform] = FavPlatform
    )
VAR TotalListeningTime = 
    CALCULATE(
        SUM('spotify_history'[listening_time_min]),
        ALL('spotify_history')
    )
RETURN
DIVIDE(
    ListeningTimeForFavPlatform,
    TotalListeningTime,
    0
)
```

- **Highlights Summary Text:** Provides a dynamic narrative summarizing key listening statistics, such as total listening time, favorite artist, and most-played track. The measure dynamically adapts to applied year filters.
```
Highlights Summary Text = 
" In " & [Listening Year or Range] & 
", you listened to " & 
IF(
    ISFILTERED('spotify_history'[year]), 
    FORMAT(
        DIVIDE(
            [Total Listening Time Min], 
            1000
        ), 
        "0.00"
    ) & "K",
    FORMAT(
        DIVIDE(
            CALCULATE(
                [Total Listening Time Min],
                REMOVEFILTERS('spotify_history'[year])
            ), 
            1000
        ), 
        "0.00"
    ) & "K"
) & 
" minutes of music. Your favorite artist was " & 
IF(
    ISFILTERED('spotify_history'[year]), 
    [Favorite Artist], 
    CALCULATE(
        [Favorite Artist],
        REMOVEFILTERS('spotify_history'[year])
    )
) & 
", and your most-played track was '" & 
IF(
    ISFILTERED('spotify_history'[year]), 
    [Most-Played Track], 
    CALCULATE(
        [Most-Played Track],
        REMOVEFILTERS('spotify_history'[year])
    )
) & "'."
```

## Impact
This project highlights the potential of combining SQL and Power BI to create a seamless workflow for data exploration and visualization. By transforming complex datasets into an interactive and visually appealing dashboard, the analysis provides a unique way to reflect on personal music preferences. It emphasizes how analytical approaches can elevate user engagement, making data both accessible and enjoyable.

The project serves as a blueprint for similar applications in other domains, such as marketing, user behavior analysis, and trend forecasting, where understanding patterns and preferences drives actionable decisions.
