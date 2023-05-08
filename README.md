Download Link: https://assignmentchef.com/product/solved-ttk4255-final-project-visual-localization
<br>
This document is for the Spring 2021 class of TTK4255 only, and may not be redistributed without permission.

Figure 1: Operating principle behind the visual localization algorithm you will implement, here shown for a model of Hovedbygget, Gløshaugen.

The final project will be conducted in the same way as the midterm project. As a reminder:

<ul>

 <li>The project can be done alone or in groups of two (no more).</li>

 <li>Your score counts toward 35% of your final grade. In groups each member gets the same score.</li>

 <li>To receive a score, you must upload a written report containing answers to the questions and requested program outputs within the deadline. The report must be a single PDF. You must also upload a zip containing your code. Only one group member needs to upload everything.</li>

 <li>You are not permitted to collaborate with other groups or copy solutions from previous years. Unlike the homework, we will look for plagiarism and non-permitted collaboration.</li>

 <li>During the project period and up to the deadline, the Piazza forum will only allow private posts. We may choose to not answer your question, but if we do we will make the answer public.</li>

</ul>

<h1>About the project</h1>

Visual localization is the problem of determining the 3D position and orientation from which a photo was taken. In this project you will implement a visual localization system for a small environment of your choice. Your system should be based on the following strategy:

<ol>

 <li><em><sub>(Done once up front): </sub></em>Create a 3D model of the environment, where each 3D point is associated with an image feature descriptor (e.g. SIFT).</li>

 <li>Given a new image, establish 2D-3D correspondences between features detected in the new image and points in the model.</li>

 <li>Estimate the camera pose using a PnP solver inside a RANSAC loop, followed by a non-linear refinement that minimizes the reprojection error.</li>

</ol>

This strategy is called structure-based localization. It is similar to SLAM, except that the mapping is done entirely up front, thereby eliminating the risks of live mapping. This is useful if the environment has a decent number of features that are persistent in their appearance and 3D position during the operational period.

Some of the tasks in Part 3 involve uncertainty analysis, which has been only briefly touched upon in the lectures. Some more background is provided in the relevant tasks, but if you find this insufficient then you are expected to consult the references (in the task text) to improve your understanding.

An extended version of the dataset from Homework 5 is provided on Blackboard. Although you must collect your own data to receive full score in Part 1 and Part 2, you may want to use this dataset initially and collect your own data later, if time allows. If you are working as a pair, then this also lets you do Part 1 concurrently with Part 2 and Part 3, which can be a natural division of work to begin with.

The assignments and projects in this course are copyrighted by Simen Haugo (<sub><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="3850594d5f57164b51555d56785f55595154165b5755">[email protected]</a></sub>) and may not be copied, redistributed, reused or repurposed without permission.

<h1>Camera calibration</h1>

To create your 3D model, you need to know the intrinsics for your camera and correct your images for lens distortion. Both can be done using either the Single Camera Calibrator App <a href="https://se.mathworks.com/help/vision/ug/single-camera-calibrator-app.html">(link)</a> from Matlab’s Computer Vision Toolbox or the example script from OpenCV’s Camera Calibration Module <a href="https://docs.opencv.org/master/dc/dbb/tutorial_py_calibration.html">(link)</a><a href="https://docs.opencv.org/master/dc/dbb/tutorial_py_calibration.html">. </a>Both of these are based on taking images of a planar pattern to estimate the intrinsics.

An extended version of the dataset from Homework 5 is provided on Blackboard. If you don’t wish to calibrate your camera, then you can use this dataset for the rest of the project instead. You will not receive points for this part, but you can still receive full score for the remaining parts (except Task 2.1).

<h2>Task 1.1</h2>

Follow the Matlab or OpenCV tutorial linked above and calibrate the camera that you intend to use. A smartphone camera should be fine. Below are some additional tips for success.

<ul>

 <li>Display the calibration pattern on your TV or computer display. Turn off the lights and shut the curtains to avoid glare. To generate a calibration pattern you can use the Calib.io website <a href="https://calib.io/pages/camera-calibration-pattern-generator">(link)</a><a href="https://calib.io/pages/camera-calibration-pattern-generator">.</a></li>

 <li>Keep the calibration pattern fully visible in each image, including the white outer border. If the software fails to find the pattern, it is usually because not enough of the outer border is visible.</li>

 <li>Include both frontoparallel images and images at 45 degrees tilt against the pattern.</li>

 <li>Lens distortion is most severe at the edges of the image. Therefore, including images with the pattern appearing near the edges is important to determine the distortion parameters.</li>

 <li>Use the same settings (focus, zoom and aperture) that you will use when collecting your data.</li>

 <li>The size of the pattern (e.g. the checkerboard squares) only affects the calibration extrinsics. When you only care about the intrinsics you can just specify an arbitrary size.</li>

</ul>

Include the following in your report:

<ul>

 <li>A bar chart of the mean reprojection error per image and a scatter plot of the 2D error vectors collected from all the images. See <sub>showReprojectionErrors </sub>in Matlab <a href="https://se.mathworks.com/help/vision/ref/showreprojectionerrors.html">(link)</a> for an example.</li>

 <li>The standard deviation of the intrinsics. In Matlab, the function <sub>displayErrors </sub><a href="https://se.mathworks.com/help/vision/ug/evaluating-the-accuracy-of-single-camera-calibration.html">(link)</a> prints these. In OpenCV, <sub>calibrateCameraExtended </sub><a href="https://docs.opencv.org/master/d9/d0c/group__calib3d.html#ga3207604e4b1a1758aa66acb6ed5aa65d">(link)</a> returns the standard deviations.</li>

</ul>

<h2>Task 1.2</h2>

Write a script that undistorts a given image, using e.g. <sub>undistortImage </sub>in Matlab or <sub>undistort </sub>in OpenCV. Use your script to analyze the effect of uncertainty in the distortion parameters: generate e.g. ten hypothetical distortion parameter vectors, by sampling from a normal distribution with the above standard deviations, and undistort an image of your choice. Comment on the differences and whether undistortion seems necessary for your camera.

<h1>Model creation</h1>

Here you will create the model containing the 3D points and their associated feature descriptors. A two-view reconstruction is sufficient for solving the tasks in the next part. You can therefore apply the strategy from HW5, given that you first extract the 2D-2D correspondences and the feature descriptors.

<h2>Task 2.1</h2>

You can collect your own dataset if you calibrated your camera. Pick a location and capture images from different viewpoints. You want two images for creating the model. Keep in mind that you need sufficient overlap to find correspondences, but also a sufficient baseline for an accurate triangulation. You also want some additional test images for the next part. It will be useful to have images taken at different distances from the modeled scene.

Make sure to undistort the images that you intend to use. Note that the undistorted image may “modify” the intrinsic matrix obtained from calibration (due to offsetting or scaling). Read the documentation for your calibration package to see if this is the case. If so, then you will want to save the modified intrinsic matrix for use in the following tasks. You don’t need to report anything for this task.

<h2>Task 2.2</h2>

Pick a pair of images and create a two-view reconstruction. Save the 3D point locations and a feature descriptor associated with each 3D point. If you use the provided dataset, then you still need to do this task to obtain the descriptors. The images in the provided dataset are undistorted.

Python users can follow OpenCV’s tutorial for feature extraction and matching <a href="https://docs.opencv.org/master/dc/dc3/tutorial_py_matcher.html">(link)</a><a href="https://docs.opencv.org/master/dc/dc3/tutorial_py_matcher.html">.</a> SIFT is still a popular descriptor and detector, so you may want to use that. Matlab users can follow the examples in the documentation for <sub>matchFeatures </sub><a href="https://se.mathworks.com/help/vision/ref/matchfeatures.html">(link)</a><a href="https://se.mathworks.com/help/vision/ref/matchfeatures.html">.</a> SURF is similar to SIFT and is probably a good choice. You are free to use HW5 or any external code to estimate the relative pose and the 3D point locations. You will receive a higher score if you refine the model by bundle adjustment (external code is OK).

Include the following in your report:

<ul>

 <li>The pair of images you used for your reconstruction.</li>

 <li>A description of how you extracted and matched features. Specify if you did anything beyond the above tutorials, such as cross-checking, bidirectional matching or using a different metric.</li>

 <li>A description of how you estimated the relative pose and the 3D point locations, including how you eliminated outlier correspondences and how you performed bundle adjustment (if you did).</li>

</ul>

<h2>Task 2.3</h2>

The reconstruction is only unique up to a rigid body transformation and a scaling factor. You can resolve the former by fixing the model coordinate frame, e.g. to one of the two cameras, but the scale is still meaningless. Impose a metric scale by measuring (or guessing) the real distance in meters between two points, and multiplying the 3D coordinates by an appropriate scaling factor. Describe how you determined this scaling factor in your report.

<h1>Localization and uncertainty analysis (8 points)</h1>

Here you will use the model to estimate the camera pose of new images (<em><sub>queries</sub></em>). Your strategy should be to establish 2D-3D point correspondences between the query image and the model via descriptor matching, then estimate the camera pose by minimizing the reprojection error. Due to the possibility of outlier correspondences, you should first estimate a rough pose and the inlier set using RANSAC.

<h2>Task 3.1</h2>

Write a script named <sub>localize </sub>that estimates the camera pose of a given query image. The script visualize_query_results is provided for visualizing your results. Estimate the camera pose of three query images of your choice. Include the generated figure for each and describe your approach (e.g. how you selected the inlier threshold and any other constants).

You probably have to modify the visualization script to work with your results, particularly the data format and the viewpoints for the pseudo-3D figures. The script will use some sample data if you run it as-is, so that you can see what the figure should look like.

You are free to use external code to solve this task, for example: <sub>solvePnPRansac</sub> (OpenCV) and estimateWorldCameraPose (Matlab). To minimize the reprojection error and further refine the pose, you can use external code or your LM implementation from the midterm project.

<h2>Task 3.2</h2>

It’s good practice to analyze the uncertainty of your estimates. For example, you may want to know how the pose estimate is affected by noise in the 2D feature locations, perhaps caused by imprecision in the keypoint detector. This requires you to estimate not only the “best” pose, but also its distribution. Due to the non-linearities involved, this distribution does not have a nice analytical form. However, if the measurements follow a Gaussian distribution, then the distribution of the estimated pose can be approximated by a Gaussian with the covariance matrix

<em>,                                                             </em>(1)

where <strong><sub>p </sub></strong>is a vector parameterizing the pose, <strong><sub>J </sub></strong>is the Jacobian of the residuals, evaluated at the least squares minimum, and <strong><sub>Σ</sub></strong><strong><sub>r </sub></strong>is the covariance matrix of all the measurements. This is an application of propagation of covariance, which is explained in Hartley and Zisserman 5.2 (available on BB). If you use a local parameterization, then <strong><sub>p </sub></strong>has three translational and three rotational components, and <strong><sub>Σ</sub></strong><strong><sub>p </sub></strong>is a 6 × 6 matrix. If you have <em>n </em>correspondences, then <strong>Σ<sub>r </sub></strong>is a 2<em>n </em>× 2<em>n </em>matrix.

Compute the covariance <strong><sub>Σ</sub></strong><strong><sub>p </sub></strong>for the three queries you used above. Assume that the measurement covariance is <strong><sub>I </sub></strong>with <em><sub>σ</sub></em><em><sub>r </sub></em><sub>= 1 </sub>pixel. Report the standard deviations of the translational and rotational pose parameters in millimeters and degrees. (The standard deviations are the square roots of the diagonal entries of <strong><sub>Σ</sub></strong><strong><sub>p</sub></strong>.)




<h2>Task 3.3</h2>

The derivation of Eq. (1) assumes that the estimated pose is the solution to a <em><sub>weighted </sub></em>least squares problem, where the objective function to be minimized is

<em>.                                                               </em>(2)

A common special case is when the measurement noise is uncorrelated from point to point, which is a reasonable model of e.g. keypoint detector noise. Then the weighted objective function simplifies to

<em>,                                                       </em>(3)

where <strong><sub>Σ</sub></strong><em><sub>i </sub></em>is the <sub>2</sub>×<sub>2 </sub>covariance matrix of point <em><sub>i</sub></em>. This can be seen as downweighting the influence of uncertain points, as their inverse covariance will be small (see Szeliski B.1). If <strong><sub>Σ</sub></strong><strong><sub>r </sub></strong>is a scalar multiple of the identity matrix, as in the previous task, then the solution to the weighted problem is identical to that of the unweighted problem, but in general these are not equal.

Re-estimate the camera poses for the three query images, this time <em><sub>with </sub></em>weighting. Assume that the point measurements are corrupted independently by zero-mean Gaussian noise, with covariance

<em> ,                                                                </em>(4)

where pixels and <em><sub>σ</sub></em><em><sub>v </sub></em><sub>= 0<em>.</em>1 </sub>pixels). Report the standard deviations of the pose parameters and discuss which parameters are most uncertain in this case.

<table width="666">

 <tbody>

  <tr>

   <td width="666">If you are using your LM implementation from the midterm project, then a simple solution is to modify your residuals function to return the weighted residuals <strong><sub>Σ</sub></strong><sup>−</sup><strong><sub>r</sub></strong><sup>1<em>/</em>2</sup><strong><sub>r</sub></strong>. The matrixcan in general be computed from the Cholesky decomposition of <strong><sub>Σ</sub></strong><strong><sub>r </sub></strong><sub>= <strong>LL</strong></sub><em><sup>T </sup></em>as <strong><sub>L</sub></strong><sup>−1</sup>. Keep in mind that <strong><sub>Σ</sub></strong><strong><sub>r </sub></strong>must match the order of the residuals in <strong><sub>r</sub></strong>. If the first <em><sub>N </sub></em>residuals are the horizontal differences and the last <em><sub>N </sub></em>residuals are the vertical differences, then <strong><sub>Σ</sub></strong><strong><sub>r </sub></strong><sub>= </sub>diag .</td>

  </tr>

 </tbody>

</table>

<h2>Task 3.4     (2 points)</h2>

Besides imprecision in the keypoint detector, you should also consider the effects of an imprecise calibration. Instead of deriving an analytical approximation of the resulting covariance <strong><sub>Σ</sub></strong><strong><sub>p</sub></strong>, here you should estimate the covariance by Monte Carlo simulation (Hartley and Zisserman 5.3):

<table width="642">

 <tbody>

  <tr>

   <td width="642"><strong>Monte Carlo estimation of covariance: </strong>– Over <em><sub>m </sub></em>trials:–    Randomly sample an intrinsic matrix <strong><sub>K </sub></strong>under the expected calibration error distribution.–    Estimate the query camera pose using <strong><sub>K</sub></strong>. (For simplicity you can use the same inlier set; i.e.you don’t need to repeat RANSAC.)–    Append the vector <strong><sub>p </sub></strong>of estimated pose parameters to a list–    Estimate <strong><sub>Σ</sub></strong><strong><sub>p </sub></strong>as the covariance of the <em><sub>m </sub></em>parameter vectors, using e.g. <sub>cov </sub>in Numpy/Matlab.</td>

  </tr>

 </tbody>

</table>

Ignore the possibility of errors in the undistortion and assume that the remaining calibration errors are given by three normally distributed random variables, <sub>(<em>η</em></sub><em><sub>f</sub></em><em><sub>,η</sub></em><em><sub>c</sub></em><em><sub>x</sub></em><em>,η<sub>c</sub></em><em><sub>y</sub></em>), with zero mean and covariance matrix <strong>Σ<sub>K</sub></strong>, such that

<strong>K</strong><em>,                                                         </em>(5)

where <strong><sub>K</sub></strong><strong>¯ </strong>is the intrinsic matrix produced by the calibration. Assume further that the calibration errors are uncorrelated, such that <strong><sub>Σ</sub></strong><strong><sub>K </sub></strong><sub>= </sub>diag. Ignore any other sources of noise and estimate the pose <em><sub>without </sub></em>weighting. Compute a Monte Carlo estimate of <strong><sub>Σ</sub></strong><strong><sub>p </sub></strong>for the following scenarios, for a single query image of your choice:

<ul>

 <li>Relatively uncertain focal length (<em>σ<sub>f </sub></em>much larger than <em>σ<sub>c</sub></em><em><sub>x </sub></em>and <em>σ<sub>c</sub></em><em><sub>y</sub></em>).</li>

 <li>Relatively uncertain horizontal principal point (<em><sub>σ</sub></em><em><sub>c</sub></em><em><sub>x </sub></em>much larger than <em><sub>σ</sub></em><em><sub>f </sub></em>and <em><sub>σ</sub></em><em><sub>c</sub></em><em><sub>y</sub></em>).</li>

 <li>Relatively uncertain vertical principal point (<em><sub>σ</sub></em><em><sub>c</sub></em><em><sub>y </sub></em>much larger than <em><sub>σ</sub></em><em><sub>f </sub></em>and <em><sub>σ</sub></em><em><sub>c</sub></em><em><sub>x</sub></em>).</li>

</ul>

The specific values for <em><sub>σ</sub></em><sub>∗ </sub>are not important; you may use e.g. <sub>50 </sub>pixels as a “large” value and <sub>0<em>.</em>1 </sub>pixels as a “small” value. <em><sub>m </sub></em><sub>= 500 </sub>trials should be sufficient. Report the standard deviations of the pose parameters and discuss which parameters are most affected in (a), (b) and (c).

<h1>Part 4       Extras</h1>

The scoring for this part will be adjusted based on everyone’s achievements. You most likely do not have to do all the tasks here to get a full score for this part, but doing just one may not be enough.

<h2>Task 4.1</h2>

The covariance matrix you obtained in Task 3.4 represents the uncertainty of an estimate made <em><sub>without </sub></em>knowledge of the calibration error distribution. If the calibration error covariance is known, then we should be able to improve the estimate. Suggest a way to incorporate <strong><sub>Σ</sub></strong><strong><sub>K </sub></strong>into the pose optimization and re-estimate the poses (a), (b) and (c) in Task 3.4. Compare the resulting standard deviations of the pose parameters with and without weighting.

<h2>Task 4.2</h2>

Create a larger model involving at least five viewpoints. You are free to use any external code. Include a description of how you created the model, and three figures showing localization in the larger model.

<h2>Task 4.3</h2>

Compare the use of different feature descriptors. Some points of comparison could for example be the number of inliers in the localization and/or model creation stage, and the reprojection error and pose uncertainty at the localization optimum.

<h2>Task 4.4</h2>

RootSIFT is a simple modification of SIFT proposed by Arandjelović and Zisserman [1]. They claim that <em>“using a square root (Hellinger) kernel instead of the standard Euclidean distance to measure the similarity between SIFT descriptors leads to a dramatic performance boost in all stages of the pipeline.” </em>Investigate the validity of their claim in your localization and/or model creation stage.

<h2>Task 4.5</h2>

Compare different RANSAC methods for the localization and/or model creation stage. See the recent survey by Jin et al. [2] and the associated video recording <a href="https://www.youtube.com/watch?v=igRydL72160">(link)</a> for some state-of-the-art RANSAC methods.

<h1>References</h1>

<ul>

 <li>Arandjelović and A. Zisserman. Three things everyone should know to improve object retrieval.</li>

</ul>

In <em>Conference on Computer Vision and Pattern Recognition</em>, pages 2911–2918. IEEE, 2012.

<ul>

 <li>Jin, D. Mishkin, A. Mishchuk, J. Matas, P. Fua, K. M. Yi, and E. Trulls. Image matching across wide baselines: From paper to practice. <em>International Journal of Computer Vision</em>, pages 1–31, 2020.</li>

</ul>