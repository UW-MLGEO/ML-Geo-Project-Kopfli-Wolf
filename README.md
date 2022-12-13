# Forecast and discover seismic velocity time series (dv/v) in California by using Machine Learning
## Scientific Motivation

### Location California
California is a state of extremes in terms of weather conditions, water use, and tectonic features (Clements and Denolle, 2022 (preprint, and references therein). There are extreme dry periods without any precipitation that last several months to years. This leads to a drop in the water table. In addition, water is pumped from the groundwater reservoir to irrigate fields and supply households. This intensive water harvesting causes the groundwater level to drop further. A sinking groundwater level causes the pore pressure to dropx. At least, the state is also affected by 'El Nino' and 'La Nina' effects from the pacific ocean. These effects lead to irregular low or high precipitation. Besides the strongly fluctuating groundwater level, earthquakes also change the structure of the subsurface. These changes cause small changes in seismic velocity (dv/v).

### Seismic Velocity Changes (dv/v)
For our project, we use ambient noise single-station cross-correlations (SSCC). This means that the individual components of a seismic station are correlated with each other (Feng and Huang, 2021, Hobiger et al, 2014, Viens et al, 2018). For noise correlations, you assume a homogeneous noise field. This means the nois sources have to be homogeneous, distributed, spatial and temporal. But Hillers and Ben-Zion (2011) found noise source correlations with temperature and wind direction (and topography) and wind speed. In this study, they used higher frequencies up to 8 Hz.  Feng and Huang (2021) used 0.1 to 0.9 Hz and could not find an ambient noise field and wind connection. This can be because of the lower frequency range. We use the frequencies up to 4 Hz and assume first an homogeneous noise source distribution. If there is time left, we will check if the California dv/v data show a wind dependency. Under the homogeneous noise source distribution assumption, we can correlate the time series component wise. If you compare long time series you are able to detect small changes or weak trends. This may reflect changes in the subsurface mechanical properties. SSCC has a smaller sensitivity foot print because all-time series leading the correlation are from the same place (same station). The results are therefore very local. Different depths can be investigated with different frequencies, the deeper the frequency, the deeper into the subsurface one can see. We will use the frequency band up to 4 Hz and thus see about 500 m deep (Clements and Denolle (2018) 0.05 to 4 Hz) and Clements and Denolle (2022, preprint) (2 to 4 Hz). This is sufficient to see the groundwater level variations. More details about the method can be found in the review article from Wang and Yao (2020).

### Project
In this project, we have two goals. First to make a prediction of dv/v and second to understand the relationships between external processes and subsurface velocity changes. 

Goal1: forecast dv/v by using last 20 years of dv/v data
We have time series showing the changes in the seismic velocities in California within the last 20 years. By training a machine learning model with these time series, we hope to predict future changes of the seismic velocities and find trends therein. The hope is that we will be able to create a temporal forecast using Deep Probabilistic Koopman so that we can compare it in performance to gated recurrent unit (GRU) neural network and recurrent neural network (RNN). 
Goal2: discover dependencies between external processes as precipitation & temperature and dv/v
For the same period when we have single-station dv/v we also have interpolated precipitation and temperature maps. By training a machine learning model (Gaussian Process Regressor (GPR)), we hope to find dependencies between the weather data and the seismic velocity changes. and probably also wind directions (and topography) and wind speed. 
First preprocessing steps
In the following figures, we see the weather data (temperature and precipitation) at 1985-05-31 and the with dots represent the seismic station positions, where we have dv/v time series. Each station lies in a grid cell of the weather map which means that the seismic station location is less precise than before but the dv/v time series and the weather data now have exactly the same location (latitude and longitude). The seismic station locations and the map do not exactly overlap (some of the stations are offshore in the black part of the figure). In reality, this is not the case. Probably, the weather maps are cut off at the coast and now it seems as if the seismic stations are offshore.

## Data Preparatien
The first idea was to build one xarray (geo- and time-referenced) with all data (seismic and weather). The problem there could be that specific dv/v data time series are only one year long whereas the weather data are more than 30 years long. All time series must have the same length, which means that the dv/v time series is mostly filled with NaN. This increases the file size. We also observe a slight shift to the left of the seismic stations compared to the weather map.

### Scientific Workflow
Develop and describe a scientific workflow using diagrams. Include steps such as data access, and data processing. Describe the GitHub project with the file structure.

#### Download Data:
- California dv/v data → DONE
- California precipitation data → DONE
- California temperature data → DONE
- Earthquake catalog

#### Data pre-processing for GPR:
- Create x-array for each data set → DONE
- Check georeferences of data set → DONE
- Merge datasets if meaningful (memory capacity increases due to NaN)
- Prepare data for one station (CC.RIO)
- Create continuous time serie (add NaN)


#### Test Model Parameter:
Discovery:
- Choose ML model: (GPR)
- Split data into test and training data set
- Test different temporal splits
- → skipped because I focused on one station
- Test different spatial splits
- I tested 20% to 90% of the data in the training data-set
- Test different hyper parameters
- Kernel: 1e-20 to 1e30 (Best fit: 26)
- Noise: 1e-20 to 1e10 (Best fit: 0.28)

Forecast: 
- Choose ML model: Deep Probabilistic Koopman, aka “time dependent analogue of Cover’s Theorem”, Recurrent Neural Network, LSTM, and GRU
- Treat similarly to  neural network
- Split Data into Training and Test Sets
- Training set should be ~80% of data
- Test data should be ~20% of data
- Compare all three models to see which one is the best performing out of all of them



#### Modeling:

Discovery:
- Run trained model with chosen model parameter
- Visualize model output
- Calculate some statistical values to quantify the model performance (still to do)
Forecast: 



#### Interpret Model Output:
What do we see?
What did we expect?
What is unexpected?
What can we learn?
What can we do to improve the model output?

## Machine Learning Model
### Gaussian Process Regressor (GPR):
The Gaussian Process Regressor (GPR) tries to fit functions through observation points. By doing this, the GPR can use several input parameters to predict an output parameter as a function of the input parameters. GPR does not need any prior knowledge and it gives an error for each prediction. As it is clearly visible in the figure from Wang (2020), the GPR is a non-linear regression model. This means, GPR tries to fit a non-linear function through the observation points. 

Functionality of Gaussian Process Regressor: The blue dashed line represents the ‘True Function’. On this line, 10 observation points were picked (black cross). The GPR fits 20 functions (thin colored line) through the observation points. The mean of these fitted functions is shown as the red fat line. (Wang, 2020)


## Results & Discussion
### Gaussian Process Regressor (GPR):
The figures below show the result of the GPR, trained with only 30% of the data. It is clearly visible that the algorithm finds a function through the actual scatter cloud, but it does only represent a sparse part of the data. The scattered real or true data can’t be represented as a function.

Compare training, test and predicted dv/v: The GPR was trained with temperature and precipitation time series at once and the input to predict the dv/v were also both parameters. So the scatter clouds are 3D but on a sheet of paper it is difficult to see all three dimensions at once.

Time Series of four and a half year: The colored lines are the input weather parameter (temperature and precipitation), the solid black line are the true dv/v  observations and the black dashed line represents the model prediction. The prediction is to noisy and the peak is not shifted slightly to the right of the temperature peak.

The prediction of the dv/v (dashed black line) not close to the test data (black solid line). Instead of showing a smoothed curve with a peak slightli after the temperature peak, the predicted dv/v looks similar to the temperature but in the range of the actual dv/v data. This could be the case because of :
- Diffusion of precipitation & temperature (Feng et al., 2021)
- Probably it helps to smooth the data before feed them into the model
- Gaussian Process Regressor not the right model neural network
- I still not found good hyperparameters
- Set completely different hyperparameter ranges to prevent the algorithm to fall into the same local minima (speculation!)
- Other parameters (not precipitation & temperature) have a significant impact on dvv → earthquake (add the eq catalog)
- I choose a bad station
- Try another station

## References
Clements, T., & Denolle, M. A. (2018). Tracking groundwater levels using the ambient seismic field. Geophysical Research Letters, 45, 6459–6465. https://doi.org/10.1029/2018GL077706

Denolle, Marine, and Tim Clements. "The Seismic Signature of California's Earthquakes, Droughts, and Floods." (2022). EarthArXiv. https://doi.org/10.31223/X5RQ0G (preprint)

Feng, K.-F.,  Huang, H.-H.,  Hsu, Y.-J., &  Wu, Y.-M. (2021).  Controls on seasonal variations of crustal seismic velocity in Taiwan using single-station cross-component analysis of ambient noise interferometry. Journal of Geophysical Research: Solid Earth,  126, e2021JB022650. https://doi.org/10.1029/2021JB022650

Hillers, Gregor, and Y. Ben-Zion. "Seasonal variations of observed noise amplitudes at 2–18 Hz in southern California." Geophysical Journal International 184, no. 2 (2011): 860-868. https://doi.org/10.1111/j.1365-246X.2010.04886.x

Hobiger, Manuel, et al. "Single-station cross-correlation analysis of ambient seismic noise: application to stations in the surroundings of the 2008 Iwate-Miyagi Nairiku earthquake." Geophysical Journal International 198.1 (2014): 90-109.https://doi.org/10.1093/gji/ggu115

Viens, Loïc, et al. "Complex near‐surface rheology inferred from the response of greater Tokyo to strong ground motions." Journal of Geophysical Research: Solid Earth 123.7 (2018): 5710-5729. https://doi.org/10.1029/2018JB015697

Wang, Q.-Y., Brenguier, F., Campillo, M., Lecointre, A., Takeda, T., & Aoki, Y. (2017). Seasonal crustal seismic velocity changes throughout Japan. Journal of Geophysical Research: Solid Earth, 122, 7987–8002.
https://doi.org/10.1002/2017JB014307

Wang, Qing‐Yu, and HuaJian Yao. "Monitoring of velocity changes based on seismic ambient noise: A brief review and perspective." Earth and Planetary Physics 4, no. 5 (2020): 532-542. https://doi.org/10.26464/epp2020048

Wang, Jie. "An intuitive tutorial to Gaussian processes regression." arXiv preprint arXiv:2009.10862 (2020).https://doi.org/10.48550/arXiv.2009.10862


Mallen, A., Lange, H., & Kutz, J. N. (2021). Deep Probabilistic Koopman: Long-term time-series forecasting under periodic uncertainties. arXiv preprint arXiv:2106.06033.
