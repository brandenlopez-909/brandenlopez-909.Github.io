---
layout: post
title:  "Portfolio Theory: Optimal rates of return, and interpolation."
subtitle: Modeling rates of return using techniques in Numerical Analysis
gh-repo: https://github.com/brandenlopez-909/RepositoryHub/tree/master/Portfolio%20Theory%20Interpolation
tags: [Data Sceince, Numerical Analysis, Finance]
comments: true

---

Saving for the future is a complicated procedure and it shows as 84% of boomers in the United States have no retirement strategy. In this project we aim to develop our financial knowledge, and future savings with portfolio theory, creating a portfolio consisting of stocks on the NYSE: TSLA, MSFT, GOOGL, AXGT and NOC; with the goal of maximizing returns, and predicting future returns with methods from Numerical Analysis. 

# Problem Statement: 
Being so young we have a high-risk tolerance, because of this we aim to have the highest expected rate of return, with little regard for variance. Furthermore, with a large amount of historical data available, we can interpolate future returns, this can tell us where to invest our money for max gains.

# Methods: 
Despite having a higher risk tolerance than most age groups, we do not to go all in on any one stock or have any one stock dominate our portfolio. In order to achieve this, we create a list of criteria we want. We want 23% of our portfolio to be in safer blue-chip stocks, 77% in more volatile stocks, no one stock being more than 30% of our portfolio, and every stock weighted at least 1%. 
With a criterion in mind, we need some way to value the stocks. Yahoo finance was used to collect the monthly closing stock price from September 2018, to September 2019. From this we calculated the monthly rates of return (equation 1), and then calculated the expected rate of return (equation 2) for each stock in the portfolio. Using the individual expected rates of return we can create an objective function and use a method from Linear Research Operations, Solver in Excel, to find the maximum Expected rate of return for our portfolio (figure 1). 
 
![Equation1](/assets/img/numerical/Equation1.png)
![Figure1](/assets/img/numerical/Figure1.png)

Despite using methods in statistics and LRO, we want to interpolate the future returns. With equation 1 we aimed to find the next month’s closing price, and thus the rate of return. First, we built a LaGrange interpolating polynomial; secondly, we created a Natural Cubic Spline Model and compared the results. While other models were considered, they were omitted due to reliance on derivatives; likewise, forward difference interpolation was a prime candidate but performed terribly immediately after the nodes the data provides.
Lastly, we used knowledge of portfolio weights, determined rates of returns, and covariance matrix to find the variance of rate of return (Equation 3)

![Equation3](/assets/img/numerical/Equation3.png)

# Results:
While the LaGrange interpolating polynomial did work, we found that like the forward difference polynomial, it often oscillated immediately after the nodes it matched. Thus, the workable model is the Natural Cubic Spline.
Natural Cubic Spline polynomials are less prone to oscillations and can handle many nodes. Because of this it interpreted the historic data with the least error. The only issue is that going more than one month(node) past the last known month, unrealistic change occurs (Shown in figure2).    
Figure 2: In this we see the last polynomial created using Natural Cubic Spline Interpolation for TSLA. That is $s_{11}\left(x\right)$ where $x_{11}<x<x_{12}$, and $x_{11}$ is the closing price for August and $x_{12}$ is the closing price for September TSLA stock.

While it would be best to interpolate long term closing prices on the stock market, we are constrained to one month past known data. Using interpolated closing price, and the portfolio weights, we are now able to view the return for October (Figure 4).

![Figure4](/assets/img/numerical/Figure4.png)

Our interpolated figures result in a negative rate of return for the month of October. Comparing this to the standard deviation of the portfolio, $\rho_p=63\%$ we find the we are roughly 0.23 standard deviations away from expected return, a very likely possibility. Looking at the results we should aim to create a portfolio that lies on the frontier curve, but it is important to note that for some months the deviation might work in our favor, creating a very desirable rate of return. 
Statistically, the results from the portfolio are plausible, further statistical analysis might prove that individually they are not. Comparing the interpolated return rates to the actual rates we have (Figure 5).


![Figure5](/assets/img/numerical/Figure5.png)
 
Thus, the interpolated rate of return, didn’t perform too poorly. 
Our models created a “small” amount of error for closing prices. In fact, the largest amount of relative error from closing price was 20%.  Despite this, relative error in rates of return was more significant; the least error was 25% while every other error was over 73%. We can conclude that by using cubic splines, we can tell the general direction the stock will go for the upcoming month but cannot accurately predict the rate of return. While our project leads to this conclusion, our models are susceptible to non-mathematic influences such from domestic and international economic, industry, and political changes. 
# Models and error:
The code is presented in a separate file and all models are contained in it. For ease, models are and respective relative errors are presented below: 

Tesla:

$$S_{11}(x)=225.610001\ -58.96300781607924(x-11)\ +\ 111.33450272411883\left(x-11\right)^2\ -37.11150090803961\left(x-11\right)^3$$
	
$$ \text{Closing error: } 18.668240052435162\% \quad	\text{Rate of return error: } 79.39231561024066\% $$
Microsoft: 

$$S_{11}\left(x\right)=137.860001+3.6521913144859712\left(x-11\right)-3.723289971728968\left(x-11\right)^2+1.2410966572429893\left(x-11\right)^3$$
$$ \text{Closing error: }2.2110609685101774\%	\quad \text{Rate of return error: } 73.0414958907797\% $$

Axovant:

$$S_{11}\left(x\right)=7.03+0.291900907206438\left(x-11\right)-1.2928513608096575\left(x-11\right)^2+0.43095045360321915\left(x-11\right)^3$$
$$ \text{Closing error: } 4.6925566343042076\% \quad	\text{Rate of return error: } 103.5714285714285\% $$

Google:

$$S_{11}\left(x\right)=1190.530029-192.498282237711\left(x-11\right)+334.6624023565664\left(x-11\right)^2-111.55413411885547\left(x-11\right)^3$$
$$ \text{Closing error: } 0.6634328828050805\% \quad	\text{Rate of return error: } 25.671724707182714\% $$
Northrop: 

$$S_{11}\left(x\right)=367.869995+37.67562337086028\left(x-11\right)-46.13341405629045\left(x-11\right)^2+15.37780468543015\left(x-11\right)^3$$
$$ \text{Closing error } 8.292672233263176\% \quad	\text{Rate of return error: }131.01754648297135\% $$


