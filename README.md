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
