Repulsor AutoAlign implementation
==========================
.. code-block:: java
	public void executeRepulsorAutoAlign() {
		Pose2d currentPose = drive.getPose();
		// Set repulsor goal and get command
		repulsor.setGoal(targetPose.getTranslation());
		SwerveSample sample = repulsor.getCmd(currentPose, drive.getRobotRelativeSpeeds(), MAX_SPEED.in(MetersPerSecond), USE_GOAL, targetPose.getRotation());

		// Extract and modify chassis speeds with additional control
		var targetSpeeds = sample.getChassisSpeeds();
		targetSpeeds.vxMetersPerSecond += repulsorTranslationController.calculate(currentPose.getX(), sample.x);
		targetSpeeds.vyMetersPerSecond += repulsorTranslationController.calculate(currentPose.getY(), sample.y);
		targetSpeeds.omegaRadiansPerSecond += repulsorRotationalController.calculate(currentPose.getRotation().getRadians(), sample.heading);

		Logger.recordOutput("AutoAlign/TranslationRepulsor X", targetSpeeds.vxMetersPerSecond);
		Logger.recordOutput("AutoAlign/TranslationRepulsor Y", targetSpeeds.vyMetersPerSecond);
		// No more race conditions :Sob:
		if (Robot.isRedAlliance) {
			drive.driveFieldRelative(-targetSpeeds.vxMetersPerSecond, -targetSpeeds.vyMetersPerSecond, targetSpeeds.omegaRadiansPerSecond);
		} else {
			drive.driveFieldRelative(targetSpeeds.vxMetersPerSecond, targetSpeeds.vyMetersPerSecond, targetSpeeds.omegaRadiansPerSecond);
		}
	}

Obstacle Detection implementation
==========================
.. code-block:: java
	public boolean willCollideWithReef() {
		Pose2d currentPose = drive.getPose();

		double t = calculateClosestPoint(currentPose, targetPose);
		interpolatedPose = new Pose2d(currentPose.getTranslation().plus(targetPose.getTranslation().minus(currentPose.getTranslation()).times(t)), new Rotation2d());

		//Too many instantiations and calculations?
		List<Translation2d> interpolatedVertices = new ArrayList<Translation2d>();
		for (int i = 0; i < ROBOT_VERTICES.size(); i++) {
			interpolatedVertices.add(ROBOT_VERTICES.get(i).plus(interpolatedPose.getTranslation()).rotateAround(interpolatedPose.getTranslation(), interpolatedPose.getRotation()));
		}

		List<Translation2d> robotEdges = List.of(
			interpolatedVertices.get(1).minus(interpolatedVertices.get(0)),
			interpolatedVertices.get(2).minus(interpolatedVertices.get(1)),
			interpolatedVertices.get(3).minus(interpolatedVertices.get(2)),
			interpolatedVertices.get(0).minus(interpolatedVertices.get(3))
		);

		//TODO: Probably better way to implement SAT here
		Translation2d perpendicularLine = null;
		List<Translation2d> perpendicularStack = new ArrayList<Translation2d>();
		double dot = 0;
		double amin = Double.NaN;
		double amax = Double.NaN;
		double bmin = Double.NaN;
		double bmax = Double.NaN;

		for (int i = 0; i < robotEdges.size(); i++) {
			perpendicularLine = new Translation2d(-robotEdges.get(i).getY(), robotEdges.get(i).getX());
			perpendicularStack.add(perpendicularLine);
		}

		for (int i = 0; i < reefEdges.size(); i++) {
			perpendicularLine = new Translation2d(-reefEdges.get(i).getY(), reefEdges.get(i).getX());
			perpendicularStack.add(perpendicularLine);
		}

		for (int i = 0; i < perpendicularStack.size(); i++) {
			amin = Double.NaN;
			amax = Double.NaN;
			bmin = Double.NaN;
			bmax = Double.NaN;

			for (int j = 0; j < interpolatedVertices.size(); j++) {
				dot = interpolatedVertices.get(j).getX() * perpendicularStack.get(i).getX() + interpolatedVertices.get(j).getY() * perpendicularStack.get(i).getY();

				if (Double.isNaN(amax) || dot > amax) {
					amax = dot;
				}

				if (Double.isNaN(amin) || dot < amin) {
					amin = dot;
				}
			}

			for (int j = 0; j < reefVertices.size(); j++) {
				dot = reefVertices.get(j).getX() * perpendicularStack.get(i).getX() + reefVertices.get(j).getY() * perpendicularStack.get(i).getY();

				if (Double.isNaN(bmax) || dot > bmax) {
					bmax = dot;
				}

				if (Double.isNaN(bmin) || dot < bmin) {
					bmin = dot;
				}
			}

			if ((amin < bmax && amin > bmin) || (bmin < amax && bmin > amin)) {
				continue;
			} else {
				Logger.recordOutput("AutoAlign/Gona hit reef", false);
				return false;
			}
		}

		Logger.recordOutput("AutoAlign/Gona hit reef", true);
		return true;
	}