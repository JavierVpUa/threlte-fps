<script lang="ts">
	import { gunStores } from '$lib/gun/gunStores';
	import { useRender } from '@threlte/core';
	import { PerspectiveCamera, WebGLRenderTarget } from 'three';
	import { cameraStores } from './cameraStores';
	import { rendererStores } from './rendererStores';

	const { activeCamera, debugCamera, sightsCamera, sightsRenderTarget } = rendererStores;
	const { sightsPosition, sightsQuat } = cameraStores;

	const x2camera = new PerspectiveCamera(5);

	const { gunObject3D } = gunStores;

	$: {
		if (!$sightsRenderTarget) {
			sightsRenderTarget.set(
				new WebGLRenderTarget(window.innerWidth * 0.5, window.innerHeight * 0.5)
			);
		}
	}

	useRender(({ scene, renderer }) => {
		// if ($activeCamera == 'eyes' && $eyesCamera) {
		// 	renderer?.render(scene, $eyesCamera);
		// }
		if ($gunObject3D) {
			const gun = $gunObject3D;
			gun.visible = false;
		}

		if ($sightsRenderTarget && $sightsCamera) {
			x2camera.position.set($sightsPosition.x, $sightsPosition.y - 0.1, $sightsPosition.z);
			x2camera.rotation.setFromQuaternion($sightsQuat);
			renderer?.setRenderTarget($sightsRenderTarget);
			renderer?.render(scene, x2camera);
		}

		renderer?.setRenderTarget(null);
		if ($gunObject3D) {
			const gun = $gunObject3D;
			gun.visible = true;
		}

		if ($activeCamera == 'eyes' && $sightsCamera) {
			renderer?.render(scene, $sightsCamera);
		}

		if ($activeCamera == 'sights' && $sightsCamera) {
			renderer?.render(scene, $sightsCamera);
		}
		if ($activeCamera == 'debug' && $debugCamera) {
			renderer?.render(scene, $debugCamera);
		}
	});
</script>
