#### `StereoVisionImuFrontend::bootstrapSpinStereo`
```
processFirstStereoFrame()               // 左右目畸变校正、特征点匹配，伪亚像素优化，计算 3D 点，重置预积分；
	- setIsKeyframe()
	- feature_detector_->featureDetection(left_frame, ...);
	- stereo_matcher_.sparseStereoReconstruction(...);
		- stereo_camera_->undistortRectifyStereoFrame(...);
			- UndistorterRectifier 根据内参计算 img -> undistorted_img 的映射矩阵【map_x_，map_y_】
			- 使用【map_x_，map_y_】校正图像
		- stereo_camera_->undistortRectifyLeftKeypoints(...)
		- sparseStereoReconstruction(...)        // 双目特征点求深度
			- 获取双目标定参数，包括内外参
			- case VisionSensorType::STEREO:
				- getRightKeypointsRectified()   // 在畸变校正后的右图中，搜索匹配点
					- searchRightKeypointEpipolar()
						- 创建一个 11x101 大小的模板 templ
						- 创建一个 比模板略大的 stripe
						- cv::matchTemplate   // TM_SQDIFF_NORMED：归一化平方差匹配。匹配为 0。
							- https://docs.opencv.org/3.4/de/da9/tutorial_template_matching.html
						- cv::minMaxLoc       // 获取像素级匹配结果
						- cv::cornerSubPix    // 在匹配结果附近 10x10，搜索亚像素角点【不是匹配优化】
		- getDepthFromRectifiedMatches(...)
			- depth = fx * baseline / disparity，结果是 Z 坐标，不是 norm
		- stereo_camera_->distortUnrectifyRightKeypoints(...)
			- 计算原始图片上的特征点位置，貌似没用；
		- stereo_frame->keypoints_3d_.push_back(...)
			- versor 在归一化平面，乘以 depth 得到 3D 坐标
	- imu_frontend_->resetIntegrationWithCachedBias();    // Initialize IMU Frontend.
		- 重置预积分
```
