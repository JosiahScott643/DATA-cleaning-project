<h1>Data Cleaning Project - Layoffs Dataset</h1>

<h2>Description</h2>
The <b>Data Cleaning Project</b> focuses on cleaning and preparing a layoffs dataset using <b>MySQL Workbench</b>. The primary goals of this project include:

<ol>
  <li><b>Removing Duplicates</b>  
    <ul>
      <li>Identified and removed duplicate records using CTE and <code>ROW_NUMBER()</code> functions.</li>
    </ul>
  </li>

  <li><b>Standardizing Data</b>  
    <ul>
      <li>Cleaned and formatted company names, industry types, and country names.</li>
      <li>Standardized date formats and trimmed unnecessary spaces.</li>
    </ul>
  </li>

  <li><b>Handling NULL and Blank Values</b>  
    <ul>
      <li>Replaced blank or irrelevant values with <code>NULL</code>.</li>
      <li>Filled missing values by referencing available information.</li>
    </ul>
  </li>

  <li><b>Dropping Unnecessary Columns and Rows</b>  
    <ul>
      <li>Dropped columns and rows that were irrelevant or contained excessive NULL values.</li>
    </ul>
  </li>

  <li><b>Data Preparation for Further Analysis</b>  
    <ul>
      <li>Prepared the cleaned dataset for further data visualization and analysis.</li>
    </ul>
  </li>
</ol>

<br />

<h2>Languages and Tools Used</h2>

<ul>
  <li><b>MySQL Workbench</b></li>
  <li><b>SQL Queries</b></li>
  <li><b>CTE (Common Table Expressions)</b></li>
  <li><b>Data Manipulation and Cleaning</b></li>
</ul>

<h2>Project Walk-through</h2>

<ol>
  <li><b>Creating a Staging Table</b>
    <p>
      A duplicate table (<code>layoffs_staging</code>) was created to perform cleaning tasks. 
      Original data was inserted for manipulation.
    </p>

<pre><code>
CREATE TABLE layoffs_staging
LIKE layoffs;

INSERT layoffs_staging 
SELECT * 
FROM layoffs;
</code></pre>
  </li>

  <li><b>Removing Duplicates</b>
    <p>
      Used a CTE to identify duplicates based on key columns.
      Deleted rows where <code>row_num > 1</code>.
    </p>

<pre><code>
WITH duplicate_cte AS (
  SELECT *, 
  ROW_NUMBER() OVER(
    PARTITION BY company, location, industry, total_laid_off, 
    percentage_laid_off, `date`, funds_raised_millions, country, stage 
    ) AS row_num
  FROM layoffs_staging
)
DELETE
FROM duplicate_cte
WHERE row_num > 1;
</code></pre>
  </li>

  <li><b>Standardizing Data</b>
    <p>
      Trimmed leading and trailing spaces in text fields. 
      Standardized industry types and country names. 
      Converted date strings to <code>DATE</code> format.
    </p>

<pre><code>
UPDATE layoffs_staging2
SET company = TRIM(company);

UPDATE layoffs_staging2
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';

UPDATE layoffs_staging2
SET country = 'United States'
WHERE country LIKE 'United States%';

UPDATE layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;
</code></pre>
  </li>

  <li><b>Handling NULL and Blank Values</b>
    <p>
      Replaced empty industry fields with <code>NULL</code> values.
      Filled missing values by referencing records with similar data.
    </p>

<pre><code>
UPDATE layoffs_staging2
SET industry = NULL 
WHERE industry = ' ';

UPDATE layoffs_staging2 AS t1
JOIN layoffs_staging2 AS t2
  ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL 
AND t2.industry IS NOT NULL;
</code></pre>
  </li>

  <li><b>Dropping Unnecessary Columns and Rows</b>
    <p>
      Removed the <code>row_num</code> column after completing deduplication.
      Deleted rows with excessive NULL values.
    </p>

<pre><code>
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;

DELETE 
FROM layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
</code></pre>
  </li>
</ol>

<h2>Final Dataset</h2>
<p>The cleaned dataset is now free of duplicates, standardized, and ready for further analysis or visualization.</p>

<h2>Example Queries</h2>

<ol>
  <li><b>Check Standardized Industry Values</b>  
<pre><code>
SELECT DISTINCT industry
FROM layoffs_staging2;
</code></pre>
  </li>

  <li><b>View Cleaned Data</b>  
<pre><code>
SELECT * 
FROM layoffs_staging2;
</code></pre>
  </li>
</ol>

<h2>Key Learnings</h2>
<ul>
  <li>Mastered the use of <b>CTEs</b> and <code>ROW_NUMBER()</code> to identify and remove duplicates.</li>
  <li>Gained hands-on experience in <b>standardizing textual data</b> and transforming date formats.</li>
  <li>Applied advanced SQL concepts to clean and prepare large datasets effectively.</li>
</ul>
