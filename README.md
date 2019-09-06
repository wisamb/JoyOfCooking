# Joy of Cooking

### Problem Introduction

This was school project that consisted of two components. For the first part, each student was to create a single data table, merging individual recipe tables that come from the book <i>Joy of Cooking</i>. The individual recipe tables were created by other students in the class. Not all files followed specifications (ie, naming convention) and so the challenge was to work with the data while dropping as few recipes and ingredients as possible.

The second part of the project consisted of statistical analysis. I had chosen to compare two methods of calculating calories and  to determine if any bias exists in either method. Method 1 calculates calories using the 4-4-9 method which uses a factor of 4 for calories from carbohydrates (CHO), 4 for calories from proteins (PRO), and 9 for calories from fat (FAT)<sup>2</sup>. Method 2 uses the Atwater factors from the USDA database, which are known to be more accurate. 

## Introduction

The book <i>Joy of Cooking</i> is one of the most popular books in the United States. It has provided recipes for many American-favorite dishes since 1936. Each edition, eight in total, has seen recipes come and go, yet some recipes appear in each of the eight editions with minor changes, if any at all.

However, controversy brewed when in 2009, Brian Wansink of Cornell University published “The Joy of Cooking Too Much: 70 Years of Calorie Increases in Classic Recipes”. In this publication, the authors conclude that “calorie density and serving sizes in recipes from The Joy of Cooking have increased since 1936”. In 2018, this paper was retracted when an investigation found that academic misconduct had taken place.<sup>4</sup>

## Part I: Data Preprocessing and Retrieval

Each student was to create tab delimited file with columns Amount, Measure, and Ingredient. Ingredient names were to match values from the USDA database (found on the <a href="https://data.nal.usda.gov/search/type/dataset" target="_blank">USDA National Agricultural Library website</a>), and an NDB_No from the USDA database was added to identify each ingredient. The recipe name and year was to be parsed from the file name. 

Immediately, I noticed there were files that did not follow naming conventions, had different naming conventions for the required columns (ie, Unit rather than Measure), did not have the minimum columns of Amount, Measure, and Ingredient, had additional columns besides Amount, Measure, and Ingredient, did not include the NDB_No column, and some files were duplicates. As often as I could I leveraged the power and efficiency of R's vectorization ability. For example, using the %in% operator, I was able to convert any nonconforming columns to specification. In addition, I retrieved NDB_No values from the USDA database using a custom function that matches the ingredient to that in the USDA database. Where matching NDB_No values were not found, I used another custom function to tokenize the Ingredient column and searched the USDA database for the closest match. There were three results in five occurrences that did not have equivalents in the USDA database table. These ingredients were dropped but not the recipe. A total of 1245 ingredients from 188 recipes were retrieved.

Entries in the Measure column also needed to be matched with the USDA database. A custom function was used to standardize and correct various Measure entries, such as “tbs” or “Tbsp” to “tbsp”. None of the ingredients and recipes were dropped from this step.

I then searched the USDA table for Measure entries to return the matching Gm_Wgt value, which is used for the calorie conversion. The Amount column was multiplied by the Gm_Wgt column to get Grams for each ingredient entry. The Grams column was then multiplied by the nutrient content (a percent) to get grams of carbohydrate, protein, and fat. These weights were multiplied by the 4-4-9 or Atwater factor to convert to calories. All three were added to determine total calories of each ingredient, and all ingredient calories were added to determine calories of each recipe.

<b>A note on missing values:</b> Not all Measure entries matched the USDA database and there were 349 NA values. Although this reduced the data by 28%, I dropped these entries due to time constraints. Furthemore, many of the Atwater factors were not provided in the USDA databasea and NA values were dropped to continue with analysis. A total of 687 ingredients and 122 recipes remained.

## Part II: Statistical Analysis

I started by looking at the summary statistics for all four conditions by year.

<table>
  <tr>
    <th>Method<br></th>
    <th>Year</th>
    <th>Minimum</th>
    <th>1st Quartile</th>
    <th>Median</th>
    <th>Mean</th>
    <th>3rd Quartile</th>
    <th>Maximum</th>
  </tr>
  <tr>
    <td>4-4-9 ingredient table</td>
    <td>1936</td>
    <td>0.00</td>
    <td>49.16<br></td>
    <td>186.20</td>
    <td>271.46</td>
    <td>350.57<br></td>
    <td>1738.30</td>
  </tr>
  <tr>
    <td>Atwater ingredient table</td>
    <td>1936</td>
    <td>1.05</td>
    <td>70.69</td>
    <td>199.48</td>
    <td>282.60</td>
    <td>374.06</td>
    <td>1782.38</td>
  </tr>
  <tr>
    <td>4-4-9 recipe table</td>
    <td>1936</td>
    <td>221.0</td>
    <td>836.3</td>
    <td>1189.1</td>
    <td>1375.1</td>
    <td>1821.0</td>
    <td>3541.5</td>
  </tr>
  <tr>
    <td>Atwater recipe table</td>
    <td>1936</td>
    <td>159.2</td>
    <td>565.3</td>
    <td>1034.6</td>
    <td>1167.5</td>
    <td>1595.3</td>
    <td>3055.9</td>
  </tr>
  <tr>
    <td>4-4-9 ingredient table</td>
    <td>2006</td>
    <td>0.00</td>
    <td>55.51</td>
    <td>208.35</td>
    <td>327.00</td>
    <td>416.70</td>
    <td>3681.13</td>
  </tr>
  <tr>
    <td>Atwater ingredient table</td>
    <td>2006</td>
    <td>1.05</td>
    <td>53.60</td>
    <td>203.58</td>
    <td>306.75</td>
    <td>407.15</td>
    <td>3879.72</td>
  </tr>
  <tr>
    <td>4-4-9 recipe table</td>
    <td>2006</td>
    <td>211.6</td>
    <td>958.2</td>
    <td>1433.8</td>
    <td>1828.0</td>
    <td>2504.5</td>
    <td>5508.0<br></td>
  </tr>
  <tr>
    <td>Atwater recipe table</td>
    <td>2006</td>
    <td>186.5</td>
    <td>659.5</td>
    <td>1171.6<br></td>
    <td>1433.2</td>
    <td>2166.6</td>
    <td>4654.7</td>
  </tr>
</table>

<b>Findings:</b> Mean and quantile values between the ingredient tables (for both years) are fairly close to one another (5-10%
difference). Mean and quantile values between the recipe tables differ (for both years) 15-30% with 4-4-9 values being consistently higher.

A scatterplot confirms this second finding and shows that the 4-4-9 method scores many recipes with higher calorie content than the Atwater method. Interestingly, a majority of the recipes that display the increase are from 2006.

<div align=center>
  <img src="/images/scatterplot.png" alt="scatterplot" class="center" width="70%"/>
</div>

### Comparison with Wansink Data

I subset the Wansink data to determine if the Wansink Data uses the 4-4-9 method or the Atwater method. The data contains 17 recipes although Wansink evaluated 18 recipes. Nevertheless, 18 recipes is a small sample from the 142 total recipes from Joy of Cooking. This small sample size introduces a bias and is a prevalent criticism of Wansink’s work.

Using boxplots, I compare the distribution of the recipes for the 4-4-9, Atwater, and Wansink methods. This plot shows that the Wansink data matches the 4-4-9 method closer than the Atwater method. This can be a source of bias since the 4-4-9 method scores recipes with higher calorie content, namely for the year 2006. 

<div align=center>
  <img src="/images/boxplot.png" alt="boxplot" class="center" width="70%"/>
</div>

## References

<sup>1</sup> ESHA Research, Nutrition: General Database. 4-4-9. *Do you use 4-4-9 (449 or 944) to calculate Calories from the grams of carbohydrate, protein and fat?* Retrieved from https://esha.zendesk.com/hc/en-us/articles/202443626-4-4-9-Do-you-use-4-4-9-to-calculate-Calories-from-the-grams-of-carbohydrate-protein-and-fat-  
  
<sup>2</sup> ESHA Research, Nutrition: General Database. *Why do I get a different amount of Calories when I use the 4-4-9 calculation?* Retrieved from https://esha.zendesk.com/hc/en-us/articles/203442937-Why-do-I-get-a-different-amount-of-Calories-when-I-use-the-4-4-9-calculation-  
  
<sup>3</sup> Oransky, Ivan. "The Joy of Cooking, Vindicated: Journal Retracts Two More Brian Wansink Papers." *Retraction Watch*, 6 Dec. 2018, retractionwatch.com/2018/12/05/the-joy-of-cooking-vindicated-journal-retracts-two-more-brian-wansink-papers/.  
  
<sup>4</sup> Wansink, Brian, and Collin R. Payne. "The Joy of Cooking Too Much: 70 Years of Calorie Increases in Classic Recipes." *Annals of Internal Medicine*, vol. 150, no. 4, 17 Feb. 2009, p. 291., doi:10.7326/l18-0647. 
