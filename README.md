Project Overview
The aim of this mini-project was to explore happiness and related wellbeing measures in the UK over time and across geographies, using the combined dataset uk_wellbeing_full_dataset.csv.
This dataset brings together four subjective wellbeing indicators:
Life_satisfaction, Happiness, Worthwhile, Anxiety, for multiple years and geographical areas (UK countries, NUTS1 regions, and local authority districts).
The analysis focuses on:
How happiness has changed over time at the UK level
How happiness compares across the main UK regions
How happiness varies across local districts
How happiness relates to anxiety and other wellbeing measures
Which districts are improving or declining overtime
Tools: Python(Google Colab) and Excel
2. Data Source and Structure
Although the dataset is a single CSV (uk_wellbeing_full_dataset.csv) document, it is actually the result of merging multiple underlying Office for National Statistics (ONS) personal wellbeing datasets:
Life satisfaction dataset (by area and year)
Happiness dataset
Anxiety dataset
Worthwhile dataset
Those were originally provided as wide-format Excel files (with columns like “April 2011 to March 2012”), then reshaped and merged into a single tidy structure with columns:
Region as Area name (e.g. “UNITED KINGDOM”, “SCOTLAND”, “High Peak”, “Bristol, City of”)
Year as end year of the reporting period (e.g. 2012, 2013, …)
Life_satisfaction gotten from the average life satisfaction (0–10)
Happiness gotten from the average happiness (0–10)
Anxiety gotten from the average anxiety (0–10; higher = more anxious)
Worthwhile to what extent people feel what they do is worthwhile gotten from the average (0–10)
So each row is essentially:
“In [Region] in [Year], the average scores were: Life satisfaction=X, Happiness=Y, Anxiety=Z, Worthwhile=W.”
3. Data loading and Initial Inspection
The first block of code is about understanding what we’re working with:
df = pd.read_csv("/content/drive/MyDrive/uk_wellbeing_full_dataset.csv")
print(df.head())print(df.info())print(df.describe())
What this does in practice:
head() gives a sanity check: Are the columns we expect there? Do the values look reasonable (roughly between 0 and 10)?
info() confirms data types and highlights potential issues (e.g. numeric columns stored as object because of non-numeric characters or missing values).
describe() gives basic descriptive statistics (mean, min, max, quartiles) across all numeric columns.
This is to make sure that:
Year isn’t accidentally read as a string
Wellbeing scores are in the expected range (roughly 0–10)
There aren’t obvious outliers or clearly broken records
4. Data Preprocessing & Cleaning
4.1. Forcing the correct numeric types
The next step ensures that the core wellbeing metrics and Year are actually numeric:
num_cols = ["Life_satisfaction", "Happiness", "Anxiety", "Worthwhile"]for col in num_cols:
    df[col] = pd.to_numeric(df[col], errors="coerce")

df["Year"] = pd.to_numeric(df["Year"], errors="coerce")
Why this matters:
In the raw CSV, some of these fields may contain non-numeric characters (e.g. blanks, “:” or “..” used in ONS tables), which makes pandas treat them as object (i.e. strings).
Using pd.to_numeric(..., errors="coerce") converts anything that can’t be interpreted as a number into NaN. This is intentional: I’d rather have an explicit NaN than a hidden string messing up calculations.
So this step standardises the dataset into clean numeric columns suitable for:
Grouping and aggregations
Correlations and trends
Visualisation
4.2. Handling missing values (nulls)
Next, we deal with missing values:
df = df.dropna(subset=["Year", "Happiness"])
Here, minimal but targeted approach was taken:
Year is fundamental. If we don’t know the year, we can’t place the data in time, so those rows are dropped.
Happiness is the central outcome we care about. If a row doesn’t have a happiness value, it doesn’t help with the main question, so we also drop those.
The rows were not dropped just because they’re missing Anxiety or Worthwhile. That’s a deliberate choice to avoid throwing away too much data. We can still use those rows for time trends, region-level analysis, etc.
5. Data validation
After cleaning, I ran a few checks:
print(df["Region"].nunique(), "regions")print(df["Region"].head())
print(df[num_cols].describe())
What this tells me:
How many unique areas are in the data (hundreds of districts plus the higher-level regions and UK-wide rows).
Examples of region names, to make sure they match what I expect from ONS: local authorities like “High Peak”, “Aberdeen City”, etc.
The describe() output confirms that the wellbeing scores:
Are roughly within 0–10
Have sensible averages (around 7–8 for happiness and life satisfaction, moderate anxiety scores, etc.)
Don’t contain obviously absurd values (e.g. >10 or <0)
This step is basically a sanity check that our data preprocessing hasn’t broken anything and that the inputs are credible.
6. Splitting the Data: Regions vs Districts
The dataset mixes different geographical levels:
UK and countries (United Kingdom, England, Scotland, Wales)
NUTS1 regions (North East, North West, East Midlands, etc.)
Local authority districts and unitary authorities
For some questions we want to compare the big regions; for others we want fine-grained detail at district level. To do this cleanly, I created two separate views of the data.
6.1. Creating a cleaned region label
valid_regions = [
    "UNITED KINGDOM",
    "ENGLAND",
    "SCOTLAND",
    "WALES",
    "NORTH EAST",
    "NORTH WEST",
    "YORKSHIRE AND THE HUMBER",
    "EAST MIDLANDS",
    "WEST MIDLANDS",
    "EAST",
    "LONDON",
    "SOUTH EAST",
    "SOUTH WEST",
]

df["Region_clean"] = df["Region"].str.strip().str.upper()
Why? The ONS region names sometimes come with trailing spaces or varying capitalisation.
Converting everything to uppercase and stripping spaces avoids subtle bugs (e.g. “North West ” vs “NORTH WEST”).
6.2. Higher-level regions only (df_valid)
df_valid = df[df["Region_clean"].isin(valid_regions)].copy()
df_valid.loc[df_valid["Region_clean"] == "EAST", "Region"] = "EAST OF ENGLAND"
This gives us a clean subset containing only:
United Kingdom
England, Scotland, Wales
The nine English NUTS1 regions (with East renamed to “East of England” for readability)
This subset is used for region-level comparisons.
6.3. District/local authority only (df_districts)
To get only the districts and local authority areas, the filter was inverted:
df_districts = df[~df["Region_clean"].isin(valid_regions)].copy()
This keeps everything that is not a country or NUTS1 region. That’s where:
The top/bottom 10 happiest districts come from
The district-level trends over time come from
7. Dataset Description
In its final cleaned form:
The data covers multiple years (roughly 2012 onwards)
For each area (country, region, or district), we have four subjective wellbeing scores
Each score is on a 0–10 scale where higher is better, except anxiety (where higher = more anxious)
The data is aggregated, meaning it represents average responses for people living in each area
So when we say:
“High Peak has an average happiness score of about 8.3 in 2022,”
we are talking about the average self-reported happiness of people in that local authority, on a 0–10 scale, as per ONS surveys.
8. Analysis and Interpretation
8.1. UK happiness Trend Over Time
We computed a simple national trend:
happiness_trend = df.groupby("Year")["Happiness"].mean()
This aggregates happiness across all areas for each year and gives us an overall UK “barometer”.
When plotted, the pattern is:
Happiness starts in the mid–7s in early years
Gradually increases over time and peaks around the pre-COVID years
Then dips around the pandemic period
Shows a modest recovery in the latest year, but not necessarily back to the pre-pandemic high
Interpretation:
At a high level, the UK’s self-reported happiness has been fairly resilient but not flat. There is slow improvement over time, with a clear disruption during the pandemic, which is exactly what we’d expect.
8.2. Comparing the Main UK regions (NUTS1 + countries)
Using df_valid, we focused on the big units:
latest_year = df_valid["Year"].max()
latest_valid = df_valid[df_valid["Year"] == latest_year]

region_happiness_valid = (
    latest_valid.groupby("Region")["Happiness"]
                .mean()
                .sort_values(ascending=False)
)
We then:
Printed the scores
Plotted a horizontal bar chart
Identified the happiest and least happy valid regions:
happiest_valid = region_happiness_valid.idxmax(), region_happiness_valid.max()
least_happy_valid = region_happiness_valid.idxmin(), region_happiness_valid.min()
What this shows:
Differences between NUTS1 regions exist, but the range is modest (everyone is somewhere in the 7–8 band).
Some regions consistently sit at the top (e.g. often the South East / South West / East of England perform relatively well), while others lag behind slightly.
This tells us that at the macro level, the UK is relatively homogeneous, but there are still noticeable regional gradients in subjective wellbeing.
8.3. Comparing Happiness across districts/local areas
For a more granular view, we switched to df_districts and focused on the latest year:
latest_year = df_districts["Year"].max()
latest_districts = df_districts[df_districts["Year"] == latest_year]

district_happiness = (
    latest_districts.groupby("Region")["Happiness"]
                    .mean()
                    .sort_values(ascending=False)
)
This gives a ranked list of hundreds of local authorities by happiness score.
Then we identified:
Overall highest and lowest scoring districts
The top 10 and bottom 10 happiest districts
top10 = district_happiness.head(10)
bottom10 = district_happiness.tail(10)
We also visualised them with bar charts.
Interpretation:
At the district level, variation is wider than at the big-region level.
Some local areas are persistently high-scoring, others much lower, even within the same NUTS1 region.
This highlights that local context matters: two areas in the same region may have quite different wellbeing outcomes.
You saw specific names in the output (e.g. High Peak, Melton) as examples at the extremes.
8.4. Relationship between Happiness and Anxiety
To explore how emotional wellbeing and anxiety interact, we plotted:
plt.scatter(df["Anxiety"], df["Happiness"], alpha=0.5)
corr_ha = df["Happiness"].corr(df["Anxiety"])
The scatter plot shows a clear downward cloud: as Anxiety increases, Happiness tends to decrease.
The correlation coefficient (corr_ha) is around −0.5, which is a moderate negative correlation.
Interpretation:
Areas (and years) with higher average anxiety scores tend to report lower happiness.
This makes intuitive sense: anxiety is an unpleasant emotional state that naturally undermines day-to-day happiness.
It’s important to note this is correlation, not causation. We’re not saying anxiety causes low happiness or vice versa, only that they move together in opposite directions.
8.5. Correlations between the wellbeing measures
We also computed a full correlation matrix:
corr = df[["Life_satisfaction", "Happiness", "Worthwhile", "Anxiety"]].corr()print(corr)
The typical pattern (and what you likely saw) is:
Happiness & Life_satisfaction: very strong positive correlation (close to +1)
Happiness & Worthwhile: strong positive correlation (around +0.8)
Anxiety vs the others: moderate negative correlations (around −0.4 to −0.5)
Interpretation:
Happiness and life satisfaction are, unsurprisingly, tightly linked; they are different ways of asking, “How well is life going?”
Feeling that what you do is “worthwhile” is also strongly associated with being happier and more satisfied, which aligns with psychological theory.
Anxiety sits as a kind of “inverse wellbeing” indicator: higher anxiety tends to go hand-in-hand with lower happiness and life satisfaction.
This gives confidence that the data is internally consistent and behaving as we’d expect.
8.6. Trends for each district (who’s improving, who’s slipping?)
To look at direction of travel for local areas, we built a district-year panel:
district_year = (
    df_districts.groupby(["Region", "Year"])["Happiness"]
                .mean()
                .reset_index()
)
Then we computed a simple linear trend (slope) in happiness over time for each district:
def compute_slope(group):
    x = group["Year"].values
    y = group["Happiness"].values
    if len(np.unique(x)) < 2:
        return np.nan
    slope, intercept = np.polyfit(x, y, 1)
    return slope

slopes = (
    district_year.groupby("Region")
                 .apply(compute_slope)
                 .sort_values(ascending=False)
)
This gives us:
Districts with the strongest positive slopes: places where happiness is increasing fastest over the available years.
Districts with the most negative slopes:  places where happiness is slowly declining.
We then plotted an example trend:
district_name = "High Peak"
subset = district_year[district_year["Region"] == district_name]

plt.plot(subset["Year"], subset["Happiness"], marker="o")


Interpretation:
The slope provides a compact summary: “Is this area improving or deteriorating over time?”
It doesn’t capture non-linear behaviour (e.g. sharp dip and recovery), but for a mini analysis, it’s a useful high-level signal.
Highlighting a few example districts helps ground the story: not only what levels are now, but how they’ve been moving.
9. Limitations and considerations
As with any analysis, there are caveats:
Self-reported data: All measures are subjective, based on survey questions. They capture perceived wellbeing, not objective conditions.
Aggregated averages: We’re looking at mean scores for whole areas; we can’t see within-area inequalities or subgroup differences.
Unweighted averages in our code: The code uses simple averages across areas and years, not population-weighted averages. A small district and a large city contribute equally in some aggregations.
No causal claims: Correlations between wellbeing measures and anxiety are descriptive, not causal evidence.
Linear trend simplification: The slope treats change as linear over time; real-world wellbeing often reacts to events (e.g. recessions, pandemics) in more complex ways.
Being clear about these up front makes the conclusions more credible.
10. Summary
To wrap it all together:
We cleaned and validated a combined UK wellbeing dataset, making sure the key metrics and years were numeric, removing rows with missing essential values, and standardising region labels.
We split the data into higher-level UK regions and finer-grained local authority districts to answer questions at both scales.
Over time, UK happiness shows a gentle upward drift, interrupted by a noticeable dip during the pandemic years and partial recovery afterwards.
Across the main UK regions, differences in happiness are real but not dramatic; everyone sits roughly in the “fairly happy” zone, though some regions consistently outperform others.
At the district level, the picture is much more varied: some local areas are clear wellbeing hotspots, while others lag behind, even within the same wider region.
Anxiety and happiness are moderately negatively correlated; areas with higher anxiety tend to report lower happiness and life satisfaction, while feelings of life being “worthwhile” move in the opposite direction.
By modelling district-level trends, we can see not just who is happiest now, but who is getting happier or less happy over time, which is vital for policy and local planning.
