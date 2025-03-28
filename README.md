# Answer 1

CREATE VIEW top_grossers AS
SELECT
    f.franchise_id,
    f.feature_id,
    f.title,
    f.year,
    (f.domestic_gross + f.international_gross) AS gross
FROM
    features f
JOIN (
    SELECT
        franchise_id,
        MAX(domestic_gross + international_gross) AS max_gross
    FROM
        features
    GROUP BY
        franchise_id
) AS max_gross_per_franchise
ON f.franchise_id = max_gross_per_franchise.franchise_id
   AND (f.domestic_gross + f.international_gross) = max_gross_per_franchise.max_gross;

# Answer 2

CREATE VIEW most_profitable AS
SELECT
    f.franchise_id,
    f.feature_id,
    f.title,
    f.year,
    ((f.domestic_gross + f.international_gross) - (2.5 * f.budget)) AS profit
FROM
    features f
JOIN (
    SELECT
        franchise_id,
        MAX((domestic_gross + international_gross) - (2.5 * budget)) AS max_profit
    FROM
        features
    GROUP BY
        franchise_id
) AS max_profit_per_franchise
ON f.franchise_id = max_profit_per_franchise.franchise_id
   AND ((f.domestic_gross + f.international_gross) - (2.5 * f.budget)) = max_profit_per_franchise.max_profit;

# Answer 3

CREATE VIEW franchise_genres AS
SELECT
    fz.franchise_id,
    fr.name,
    COUNT(*) AS num_films,
    ROUND(AVG(g.comedy), 4) AS comedy,
    ROUND(AVG(g.romance), 4) AS romance,
    ROUND(AVG(g.action), 4) AS action,
    ROUND(AVG(g.fantasy), 4) AS fantasy,
    ROUND(AVG(g.horror), 4) AS horror
    -- Add more genres here as needed
FROM
    features fz
JOIN
    feature_genres g ON fz.feature_id = g.feature_id
JOIN
    franchises fr ON fz.franchise_id = fr.franchise_id
GROUP BY
    fz.franchise_id, fr.name;

# Answer 4

CREATE VIEW genre_labels AS
SELECT feature_id, genre
FROM (
    SELECT
        feature_id,
        genre,
        score,
        RANK() OVER (PARTITION BY feature_id ORDER BY score DESC, genre) AS rnk
    FROM (
        SELECT feature_id, 'action' AS genre, action AS score FROM feature_genres
        UNION ALL
        SELECT feature_id, 'comedy', comedy FROM feature_genres
        UNION ALL
        SELECT feature_id, 'romance', romance FROM feature_genres
        UNION ALL
        SELECT feature_id, 'fantasy', fantasy FROM feature_genres
        UNION ALL
        SELECT feature_id, 'horror', horror FROM feature_genres
        UNION ALL
        SELECT feature_id, 'historical', historical FROM feature_genres
        UNION ALL
        SELECT feature_id, 'espionage', espionage FROM feature_genres
        UNION ALL
        SELECT feature_id, 'mystery', mystery FROM feature_genres
        -- Add more genres here if needed
    ) AS unpivoted
) AS ranked
WHERE rnk = 1;


