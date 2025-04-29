Overview
Building upon the previous capstone work, which focused on data aggregation, cleaning, processing, and visualization, this phase aims to develop machine learning models to predict hourly bike rental counts. The project will cover feature engineering, scaling, and regression modeling techniques to build an effective predictive model.

Project Statement

Develop an end-to-end machine learning pipeline to forecast hourly bike rentals using various regression techniques and performance evaluation methods

Input dataset: DatasetLinks to an external site.

Steps to Perform

Task 1: Feature engineering (45 mins)

 Analyze the provided dataset and select relevant features
Create new features such as: 
Interaction features
Encode categorical variables and handle missing values
Scale the numerical features using StandardScaler
Save the processed dataset as "bike_rental_features.csv"
      Task 2: Model building (75 mins)

Implement various regression models including: 
Linear Regression
Ridge Regression (L2 Regularization)
Lasso Regression (L1 Regularization)
Elastic Net Regression
 Perform hyperparameter tuning using GridSearchCV
Evaluate model performance using: 
Mean Absolute Error (MAE)
Mean Squared Error (MSE)
R-squared (R²)
      Task 3: Model building with polynomial features (45 mins)

Create polynomial features for selected numerical columns
Train models with polynomial features to capture non-linear relationships
Compare results with linear models to assess improvements
Save the best-performing model
      Task 4: Model evaluation and validation (45 mins)

Perform cross-validation techniques to validate model performance (on both models- With Polynomial Features and without Polynomial Features)
Assess models using test data
Compare results across different regression models
      Task 5: Reporting and insights (30 mins)

1.    Summarize findings and key takeaways from the analysis
2.    Discuss feature importance and business implications
3.    Provide recommendations for further improvements


## Task 5: Reporting & Insights

- **Summary Findings & Key Takeaways**  
  - Recap top model performance:
    
   - **Recap top model performance**  
  - **Baseline linear models – R² ≈ 0.55**  
    A purely linear combination of all our features (time of day, temperature, humidity, etc.) explains about 55% of the hour-to-hour variability in bike rentals. The remaining 45% of the variability isn’t captured by just straight-line (linear) effects.  

  - **Cubic Ridge (degree=3) – R² ≈ 0.77 on the hold-out test set, 0.79 under shuffled CV**  
    By expanding every numerical feature into its square, cube, and all pairwise/three-way products, then fitting a Ridge (L2-penalized) model, we can capture curved and interacting effects. That boosted our explained variance from 55% up to 77% on the single test split, and 79% on average across random 5-fold splits.

    - Time-series CV revealed over-fitting (negative R2) without temporal features  
 
    
    - Where non-linear terms (PolynomialFeatures) helped: Captured complex weather/time interactions that a pure linear model missed (hence R² jump from 0.55 to 0.77).

    - Where non-linear terms (PolynomialFeatures) failed: Lacked a context for the time of day in order to apply that to a prediction. (e.g. “What was yesterday’s demand at this hour?”), so it over-extrapolated when tested on future data blocks, leading to negative R² under TimeSeriesSplit  Also the MSE went crazy as compared to shuffled CV.  So it was not generalizing well.
   
      
- **Feature Importance & Business Implications**  
  - To find out which factors really move bike rentals, we can either look at the size of each fitted weight in our Ridge model or—if we’re using a more complex model—randomly shuffle each feature and see how badly the model’s performance suffers; whichever features cause the biggest drop are our top ‘drivers.’
    - Likely top features: hour of day, temperature x humidity, lagged counts (once added).  
  - Translate to business:  
    - "Peak rentals occur around 8 AM and 5 PM on weekdays."  
    - "Comfort index (temperature x humidity) strongly predicts demand - plan for extra bikes on mild, dry days."

- **Provide Recommendations for Further Improvements**  
  1. **Inject Time-Series Features**  
     - Add `lag_1h`, `lag_24h`, rolling averages to capture autocorrelation.  
  2. **Test Tree-Based Models**  
     - Use RandomForest or XGBoost to handle non-linearities and time-lag features.  
  3. **Simplify Polynomial Features**  
     - If over-fitting persists, limit degree to 2 or remove high-order interactions.  
  4. **Operationalize the Model**  
     - Retrain weekly as new data arrives.  
     - Deploy as a scheduled job that outputs hour-ahead forecasts to your operations dashboard.  
