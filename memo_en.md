# Allan Variance

## Contents
- Gyro characteristics
- Steady state analysis
  - Sensitivity analysis of each parameter
  - Evaluation by orbit data
- About quantization error


## Gyro characteristics
- PSD : Power Spectral Density
![](2021-11-22-10-41-22.png)

![](2021-11-22-11-02-46.png)

The PSD contains a frequency-independent white noise component, which is dominant in the high-frequency range.
### ARW(Angle Random Walk)
When integrated over time to obtain the angular orientation, white noise of rate becomes a random walk of angle ($1/f^2$ random process), which is called Angle Random Walk (ARW) and expressed in deg/√hour units.

### flicker
It depends on the electrical circuitry and is called Pink noise, which is apparently obtained by applying a low pass filter to the white noise.
This value is the minimum in the Allan variance and is called Bias Instability. Unit is deg/s or deg/hour.
![](2021-12-06-10-14-18.png)

### ARRW
It is also called as red noise．PSD of $1/f^2$．Angular rateのrandom walk．
In rate gyroscopes, 1/f 2 noise is typically referred to as the Angle Rate Random Walk (ARRW) and quantified in deg/s*√Hz, deg/hour*√Hz, or, equivalently deg*√hour units.
![](2021-12-06-10-13-42.png)


Upon integration, the three rate noise sources with $1/f^0, 1/f^{+1}$, and $1/f^{+2}$ densities produce drifts of angle with $1/f^{+2}, 1/f^{+3}$, and $1/f^{+4}$ power spectral densities, respectively.

Looking at the measured data, the dominant component is the quantization error, which is also the part that should be taken into account considering the STT update frequency (1s).
![](2021-12-07-10-48-59.png)

## Evaluation of achievable steady state accuracy
### Analytic Steady-State Accuracy Solutions for Two Common Spacecraft Attitude Estimation
A fourth-order equation appears. Perhaps this is derived from the convergence conditions of the error covariance matrix.

In gyro observables (angular velocities), Gyro's observational model is
$\dot{\theta} = \omega_m - w - n_v$
The following is an example. where $w$ is the gyro drift rate (modeled as the integral of white noise, which corresponds to ARRW as a Gyro property).

n_v$ is white gaussian noise, which corresponds to ARW as a Gyro property.
**BIAS INSTABILITY is not formulated in this model. **
Is this because Bias instability (1/f noise) cannot be modeled by Gaussian and therefore cannot be handled by Kalman filter?
It seems that 1/f noise cannot be removed by filters.

Specification of DMU30-01
![](2021-12-13-10-32-56.png)
- bias repeatability???

ARW: typical <0.02[deg/$\sqrt{h}$]，max 0.04[deg/$\sqrt{h}$]
- But there are typical 0.05[deg/s rms] and max 0.1[deg/s rms] as wide-band noise. What is this? It may be a normal rms calculation, but I don't know what time range it is.
- I see from the plot $N = 2.18E-4$ or so?

ARRW is not mentioned in the specification, so it is confirmed from an Allan variance plot.
- If $K$ is the rate random walk coefficient, then $\sigma^2(t)=\frac{K^2\tau}{3}$. So the value at $\tau=3$ is $K$. (There are other error factors added here, which need to be removed.)
- From the powerpoint plot, it is roughly 2.8E-4. <- Since other terms seem to dominate, let's look at the value with the -1/2 slope considered. -> 1.0E-5 or something like that.

The method of this paper．
- For ARW, the paper's characterization is $\sigma_v = 0.02, 0.04(max)$.
The observation model for STT is
$$y_{t+1} = \theta_{t+1} + n_{t+1}$$
The observation error is a Gaussian with mean 0 and variance $\sigma_n$. The variance of Stt is the same as in the paper and is set to 20[deg/s].
The assumption of a steady state implies that the Filter has converged.

The calculation with the appropriate conditions was as follows.
$$
\sigma_\theta(-) = 5.594 [arc-s] \\
\sigma_\theta(+) = 5.388 [arc-s] \\
\sigma_w(-)      = 0.177 [arc-s/sec] \\
\sigma_w(+)      = 0.176 [arc-s/sec]
$$
However, the plot shows that the quantization error term is predominant in the shorter time part, which is not taken into account in this model, so I am not sure if it can be applied.
Also, time step in this method is the frequency of STT observations. This seems to be fine in terms of determining the accuracy of the filter. And the accuracy before the update.
At any rate, the accuracy that can be achieved when the filter converges.
The frequency of this determination is the same as the frequency of the STT observations, so it is not possible to estimate the accuracy that can be determined for each time step in Gyro.


### Analytic Steady-State Accuracy of a Spacecraft Attitude Estimator
Conditions of analysis
- ARW
  $\sigma_v = 0.025 deg/\sqrt{h}$ ($\sigma_v = 0.02 deg/\sqrt{h}$ in gyro for case study)
- ARRW 
  $\sigma_u = 3.7 \times 10^{-3} deg/h^{2/3}$ ($\sigma_u = 2.1 \times 10^{-2} deg/h^{2/3}$? in gryo for case study)
- Star tracker measurement noise
  $\sigma_n = 15 \mu rad$ ($3.09$ [arc-s])
- Gyro angular measurement noise
  $\sigma_e = 15 \mu rad$ ($3.09$ [arc-s])
![](2021-12-21-10-26-41.png)

(The achieved accuracy order is about 20$\mu rad$ → 4.1 arc-sec?)
- The single-dotted line is the result of using an approximation formula that is valid where the update frequency by STT is fast (when T is sufficiently small). As shown in the graph, the solid line coincides with the solid line in the region below $10^0$, but gradually moves away from the solid line above $10^0$.
- This paper uses a satellite dynamics model to account for white noise (called readout noise or electronic noise) during angular output. The dynamics of $\omega$ (the angular velocity of the satellite) is approximated by a random walk
$$ \dot{\omega} = n_w $$
n_w$ is white noise with standard deviation $\sigma_w$. The dynamics at this time can be anything that can be expressed in the equation of state, and the objective is to leave $\omega$. Without the dynamics of the spacecraft angular velocity $\omega$, the
$$ \dot{\theta} = \dot{\phi} - b -n_v \dot{\phi}
\dot{b} = n_u $$
and the equation of state for $\phi$ (angle of propagation in Gyro) cannot be expressed. →The angle output by Gyro and the propagation angle maintained by Gyro are equivalent.
On the other hand, we are trying to obtain steady-state accuracy, which is estimated based on Gyro's observables without using knowledge of the dynamics of the spacecraft angular velocity ($\omega$). This is for simplicity (but it is difficult to model the exact dynamics, so is this practical? (The article was written in Japanese.). ← This is called the dynamic replacement mode in other papers, and two reasons are given why this is more practical. One is that Gyro information is more accurate than orbital attitude motion and torque information. The second is that although it would be more accurate if it could use knowledge of the dynamics model, it is not practical given the cost of on-board computation.
We are considering when the value of $\sigma_w$ is large compared to other observational or process noise (i.e., when there is a large uncertainty in our knowledge of the dynamics), and we separate the terms in which $\sigma_w$ is relevant from the others. Accuracy is sought through a formulation that is independent of $\sigma_w$.
- What is called readout noise above corresponds to $\sigma_e$, which is modeled as Gyro's angle observation noise, which is the difference from the old paper. For Gyro, where angular output is available, this method can be applied as is, but in general it is not, and integration is done by OBC. Therefore, the concept is slightly different.

### Sensitivity analysis of ARW
![](analysis/plot_steady_state/sigma_theta_to_ARW.png)
- $\sigma_u = 1.0\times 10^{-5} [\text{deg/s/sqrt{s}}]$ (ARRW)
- Line color indicates STT observation accuracy ($3\sigma$[arc-s])
As the STT observation accuracy increases, the difference between before (dotted line) and after (solid line) the update increases. This is because the accuracy of the observables obtained by STT is good, so the improvement in accuracy obtained by the filter update is significant. On the other hand, the change in accuracy before the update with increasing ARW is almost independent of the STT accuracy. It can also be seen that in the region $\sigma_v < 10^{-2}$ there is almost no effect.

### Sensitivity analysis of ARRW
In the old paper's formulation with $\sigma_u = 0$, we find that
$$ s_{11} = \frac{1}{2}\left\{ \sigma_v^2T + \sqrt{\sigma_v^2T(\sigma_v^2T + 4\sigma_n^2)}
p_{11} = \frac{s_{11} \sigma_n^2}{s_{11} + \sigma_n^2} $$
and using the same spec values as above, we get
$$
\sigma_\theta(-) = \sqrt{s_{11}} = 4.973 [arc-s] }
\sigma_\theta(+) = \sqrt{p_{11}} = 4.826 [arc-s] \sigma_\theta(+)
\sigma_w = 0
$$
The effect of this is surprisingly large.
Plotting the accuracy of attitude determination when $\sigma_u$ is varied (1 second frequency)
- pre measurement update
![](analysis/plot_steady_state/sigma_theta_pre_update.png)
- post measurement update
![](analysis/plot_steady_state/sigma_theta_post_update.png)
It is almost converging to the value obtained by the above equation, and it is so. If $\sigma_u (ARRW) < 10^{-6}$, there is almost no sensitivity, but if it is larger than that, the sensitivity is large.
Incidentally, the value I read from the plot this time was $\sigma_u = 5.77\times 10^{-6}$, so there was a slight influence.


![](analysis/plot_steady_state/sigma_theta_to_ARRW.png)
- $\sigma_v = 0.01 [\text{deg/sqrt{h}}]$ (ARW)
- The color of the line indicates the observed accuracy of STT ($3\sigma$[arc-s])
Dashed line is standard deviation before filter update, solid line is standard deviation after filter update
In regions with high STT accuracy (1~15[arc-s]), the sensitivity with respect to ARRW is not large; ARRW is never on the datasheet, so ARRW can be ignored when using STT in these regions. In addition, the amount of accuracy improved by filter updates is nearly constant, independent of the observed accuracy of the STT.i
Below $\sigma_u < 10^{-6}$, ARRW has almost no effect.

### Sensitivity analysis of STT measurement interval
Specification of RPU30
- ARW: 0.006 [deg/$\sqrt{h}$]
![](analysis/plot_steady_state/sigma_theta_to_STT_time.png)
![](analysis/plot_steady_state/sigma_theta_to_STT_time_zoom.png)

Specification of Gyro using in GRUS
![](analysis/plot_steady_state/sigma_theta_to_STT_time_GRUS_compare.png)
- The solid line is the model considering Gyro angle output noise.
- Dashed line is the old formulation
- Black line is target accuracy (4.39 [arc-sec])

In the 2000 paper, RIG's Gyro angle output noise is taken into account, and there are two stages of updating: updating based on frequently obtained Gyro observables and updating based on STT observables. In particular, the update of the estimator when STT observations are obtained, as shown in equation 66, is in two steps, but when $\sigma_e = 0$, it is in one step.
It says that the Filter will match the old one if this Gyro angular output noise is not taken into account, and I followed the formulation and found exactly the same result with $\sigma_e = 0$. (The reason the plotted results did not match was that the newer results were a plot of variance, not standard deviation.)

I also plotted the same data on other axes of comparison (horizontal axis of accuracy of ARW, ARRW, etc.), but the difference from the old paper only appears when the frequency of STT observations is taken as the horizontal axis, and the rest is the same. It is difficult to assume that there is no noise in the calculated angle information. However, the quantization error is not Gaussian and cannot be handled by the Kalman filter. Therefore, it is likely that the variance of the quantization error will directly affect the accuracy. (It doesn't look that big.)


### Evaluation by orbit data
- Where should I put the STT observation error?
Since the mounting angle is off the Body coordinate system, it is necessary to consider its direction.
Actual STT observation information is the direction in which the STT is facing in the inertial coordinate system. This is expressed in terms of Euler angles. In this case, the direction of the STT is taken from the body coordinate system as azimuth around the z-axis and elevation around the y' axis.
Since the variance of the observation error is different in the line-of-sight direction and the direction perpendicular to the line-of-sight direction, let $\sigma_d$ and $\sigma_v$ be the observation noise entering each axis and $w_\phi, w_\theta, w_\psi$ respectively.
$$ E[w_\phi^2] = \sigma_d^2 \sigma_d^2
   E[w_\theta^2] = E[w_\psi^2] = \sigma_t^2 $$
The following is the result.
So the observation equation for STT is
$$ \vec{\theta_i^{stt}} = h(\vec{q_b^i}) + \vec{w_{stt}} $$
The real data $\vec{q_b^i}$ of STT can be converted to $\vec{\theta_i^{stt}}$ and given as an observed quantity (there is already observation noise in this). But when it is converted to quaternions, the observed noise of the other axes is also introduced, so what happens if it is converted again?
It is difficult to determine which sensor has the best accuracy for which axis in the data from the three STTs, but we will evaluate the accuracy of attitude determination around the y-axis using STT0, which has an axis in the body coordinate system that coincides with the line-of-sight orthogonal direction. **

- Handling of Gyro Observation Data
The observed data are angular velocities around each axis in the body coordinate system. In this case, only the angular velocity data around the y-axis is considered, so the angular velocity data is used.

- Replacement of the equation of state in the paper
In the paper, the posture is the angle around an axis in the body coordinate system. This can be expressed as an Eulerian angle in the body coordinate system if the angle is with respect to the body coordinate system at a certain time. In other words, the

\theta = \theta_b^i(t) - \theta_0 $$
Therefore, if $\theta$ is used to express the equation of state as an attitude quaternion, the equation of state can be written in quaternion (the quantity of state is $\vec{q_b^i}$), and the evaluation of accuracy can be done by adjusting the angle. We need to think about how to model it.
I thought that it would be easier to use the same formulation as in the paper, but since it is a single-axis model, it seems easier to keep it simple.

![](2022-02-08-09-31-43.png)


- What to do with the true value
  Since the line-of-sight orthogonal direction of STT0 is coincident with the y-axis, the angle accuracy around the y-axis can be considered high enough. The fitted version of this data is the true angle around the y-axis.
  On the other hand, the observables used in the estimation system are stt1 (the one whose line of sight is close to the y-axis).

- Estimates achievable for the data we are evaluating
The observation information used is $3\sigma=$600arcsec. The frequency of observation is 2 seconds, so it is expected to be about 40 arcsec.
![](plot_steady_state/../analysis/plot_steady_state/sigma_theta_to_STT_time_600arcsec.png)

## About quantization error
! [](2021-12-20-11-09-47.png)
Considering that Gyro's observation frequency is 200Hz, the required attitude frequency of 20Hz satisfies $f<\frac{1}{2\tau_0}$ with a margin, so PSD is modeled by the lower equation.
The Allan variance is
! [](2021-12-20-11-52-42.png)
Can we assume that this part is simply given as the observation error?
- [Modeling and reduction method of quantization error](http://leo.ec.t.kanazawa-u.ac.jp/~nakayama/edu/file/signal_proc_ch8.pdf)
- Dithering: Pre-mixing of weak random errors into an analog signal during quantization. This is said to reduce quantization errors.
- It seems impossible without doing something at the circuit level, and it doesn't seem possible to do anything with the digital output once it has been output?
### Survey on quantization error of each gyro

|  Gyro  |  sample rate[Hz]  |  bias instability[deg/h]  | ARW[deg/$\sqrt{h}$] | quantization error |
| --- | --- | --- | --- | --- |
| STIM210  | 2000 | 0.3 | 0.15 | none |
| ADIS16495-1  | 4250 | 0.8 | 0.09 | none |
| DMU30-01 | 200 | 0.2 | 0.04 | exist |
| M-G370PDS0 | 2000 | 0.8 | 0.03 | unknown |
| MOTUS | 1000 | 0.2 | 0.125 | unknown |
| MS-IMU3050 | 800 | 0.3 | 0.065 | none |
| MPC-MEMS-IRU | >100? | 0.1 | 0.01 | none |
| RPU30 | 100 | 0.04 | 0.006 | exist |

**Allam variance plot**
- STIM210
![](2021-12-28-09-56-07.png)
- ADIS169495-1
![](2021-12-28-10-12-16.png)
- DMU30-01
![](2021-12-28-10-15-41.png)
- M-G370PDS0
No graph of Allan variance
- MOTUS
No graph of Allan variance
- MS-IMU3050
![](2021-12-28-10-34-59.png)
- MPC-MEMS-IRU
![](2021-12-28-10-41-50.png)
- RPU30
![](2021-12-28-10-49-32.png)

I have the impression that there are not so many that show a slope of -1 at around 100 Hz for practical use.

