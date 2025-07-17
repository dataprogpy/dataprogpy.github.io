---
icon: material/numeric-6
---

# Conclusion & Next Steps


Congratulations on completing this introduction to time series analysis! You now have a solid framework for tackling time series problems:

* You started by **exploring** and **decomposing** data to understand its core structure, identifying key components like trend and seasonality.
* You learned how to **prepare** data for modeling by handling stationarity through differencing and engineering time-based features for machine learning models.
* You built and compared different types of models, like **ARMIA**, **SARIMA**, **AutoARIMA**, and **GARCH**.
* Finally, you learned how to rigorously select models using cross validation and **evaluate** your models using various metrics. 

This end-to-end process gives you the ability to turn historical time-ordered data into valuable insights and predictions.

---
## **Next Steps for Your Learning Journey 🚀**

Time series forecasting is a deep and fascinating field. This lesson provides a strong foundation, and here are some specific ways you can continue to build on it.

### **Apply These Techniques to a New Project**

The best way to solidify your knowledge is to apply it. Find a time series dataset relevant to your work or interests—such as your company's sales data, website traffic, or the stock price of a company you follow—and perform the full end-to-end workflow we demonstrated in the case study. Challenge yourself to build both a statistical and a machine learning model and compare their backtested performance.

### **Deepen Your Knowledge of the Models**

We covered the "what" and "why" of several models, but there is always more to learn about their inner workings.

* **Specific Suggestion**: Read the user guide for the [StatsForecast](https://nixtla.github.io/statsforecast/) library or the [statsmodels time series analysis section](https://www.statsmodels.org/stable/tsa.html). Also, explore other popular forecasting models we didn't cover, such as Meta's **Prophet** model, which is known for its ease of use and ability to handle holidays.

### **Explore Advanced Evaluation Metrics**

While RMSE and MAE are excellent general-purpose metrics, different business problems often require different ways of measuring forecast accuracy.

* **Specific Suggestion**: Research and implement these two metrics:
    1.  **MASE (Mean Absolute Scaled Error)**: This is a great scale-independent metric that compares your model's forecast error to the error of a naive seasonal baseline. It helps answer the question, "Is my complex model actually better than a very simple one?"
    2.  **Quantile Loss**: This metric is used when you care about the accuracy of your **prediction intervals**, not just a single point forecast. It is essential for problems where understanding the range of uncertainty is critical, such as in inventory management or financial risk assessment.