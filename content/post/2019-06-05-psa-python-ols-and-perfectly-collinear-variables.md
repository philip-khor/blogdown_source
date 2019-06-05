---
title: 'PSA: Python, OLS and perfectly collinear variables'
author: "Philip Khor"
date: '2019-06-05'
slug: psa-python-ols-and-perfectly-collinear-variables
categories: ["econometrics"]
tags: []
---


Unlike most implementations of linear models (e.g. Stata, R), Python packages don't usually drop perfectly collinear variables. 

Here's Statsmodels as a first example: (see https://github.com/statsmodels/statsmodels/issues/3824)


```python
import numpy as np 
import statsmodels.formula.api as smf
import pandas as pd 
```


```python
e = np.random.normal(size = 30)

# creating two variables x and collinear 
# where collinear is just 2 times x
x1 = np.arange(30)
x2 = 2 * x1

y = 2 * x1 + e
data = pd.DataFrame({"y": y, "x1": x1, "x2": x2})
```


```python
model = smf.ols("y ~ x1 + x2", data = data)
res = model.fit()
res.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>            <td>y</td>        <th>  R-squared:         </th> <td>   0.998</td> 
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.998</td> 
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>1.354e+04</td>
</tr>
<tr>
  <th>Date:</th>             <td>Tue, 04 Jun 2019</td> <th>  Prob (F-statistic):</th> <td>3.80e-39</td> 
</tr>
<tr>
  <th>Time:</th>                 <td>20:23:08</td>     <th>  Log-Likelihood:    </th> <td> -35.185</td> 
</tr>
<tr>
  <th>No. Observations:</th>      <td>    30</td>      <th>  AIC:               </th> <td>   74.37</td> 
</tr>
<tr>
  <th>Df Residuals:</th>          <td>    28</td>      <th>  BIC:               </th> <td>   77.17</td> 
</tr>
<tr>
  <th>Df Model:</th>              <td>     1</td>      <th>                     </th>     <td> </td>    
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>    
</tr>
</table>
<table class="simpletable">
<tr>
      <td></td>         <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th> <td>    0.3681</td> <td>    0.288</td> <td>    1.277</td> <td> 0.212</td> <td>   -0.222</td> <td>    0.959</td>
</tr>
<tr>
  <th>x1</th>         <td>    0.3973</td> <td>    0.003</td> <td>  116.364</td> <td> 0.000</td> <td>    0.390</td> <td>    0.404</td>
</tr>
<tr>
  <th>x2</th> <td>    0.7946</td> <td>    0.007</td> <td>  116.364</td> <td> 0.000</td> <td>    0.781</td> <td>    0.809</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td> 3.352</td> <th>  Durbin-Watson:     </th> <td>   2.071</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.187</td> <th>  Jarque-Bera (JB):  </th> <td>   2.993</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-0.715</td> <th>  Prob(JB):          </th> <td>   0.224</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td> 2.407</td> <th>  Cond. No.          </th> <td>7.31e+16</td>
</tr>
</table><br/><br/>Warnings:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.<br/>[2] The smallest eigenvalue is 8.01e-30. This might indicate that there are<br/>strong multicollinearity problems or that the design matrix is singular.



Neither does the popular machine learning package Scikit-Learn: 


```python
from sklearn.linear_model import LinearRegression
lm = LinearRegression()
lm.fit(X = data[["x1", "x2"]], y = data.y)
lm.coef_
```




    array([0.39728922, 0.79457845])


