### 실습화면

![image](https://github.com/qkrgudals1030/graphics/assets/50895124/ddee6f09-4b6c-4095-9cfb-1f980ccc0a46)

![image](https://github.com/qkrgudals1030/graphics/assets/50895124/ec5da9f2-4351-4477-8ee6-634742235f1a)

![image](https://github.com/qkrgudals1030/graphics/assets/50895124/fabb9a35-af75-42c7-b86f-7421e985c2b4)

### 실습코드

#### custom_geometry실습코드
```
import * as THREE from '../build/three.module.js';
import {OrbitControls} from '../examples/jsm/controls/OrbitControls.js';
import {VertexNormalsHelper} from '../examples/jsm/helpers/VertexNormalsHelper.js';

class App {
	constructor() {
		const divContainer = document.querySelector('#webgl-container');
		this._divContainer = divContainer;

		const renderer = new THREE.WebGLRenderer({antialias: true});
		renderer.setPixelRatio(window.devicePixelRatio);
		divContainer.appendChild(renderer.domElement);
		this._renderer = renderer;

		const scene = new THREE.Scene();
		this._scene = scene;

		this._setupCamera();
		this._setupLight();
		this._setupModel();
		this._setupControls();

		window.onresize = this.resize.bind(this);
		this.resize();

		requestAnimationFrame(this.render.bind(this));
	}

	_setupControls() {
		new OrbitControls(this._camera, this._divContainer);
	}

	_setupCamera() {
		const width = this._divContainer.clientWidth;
		const height = this._divContainer.clientHeight;
		const camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 100);
		camera.position.z = 2;
		this._camera = camera;
	}

	_setupLight() {
		const color = 0xffffff;
		const intensity = 1;
		const light = new THREE.DirectionalLight(color, intensity);
		light.position.set(-1, 2, 4);
		this._scene.add(light);
	}

	_setupModel() {
		const rawPositions = [
			-1, -1, 0,
			1, -1, 0,
			-1, 1, 0,
			1, 1, 0,
		];

		const rawNormals = [
			0, 0, 1, 
			0, 0, 1,
			0, 0, 1,
			0, 0, 1,
		];

		const rawColors = [
			1, 0, 0,
			0, 1, 0,
			0, 0, 1,
			1, 1, 0,
		];

		const rawUvs = [
			0, 0, 
			1, 0,
			0, 1,
			1, 1,
		];

		const positions = new Float32Array(rawPositions);
		const normals = new Float32Array(rawNormals);
		const colors = new Float32Array(rawColors);
		const uvs = new Float32Array(rawUvs);

		const geometry = new THREE.BufferGeometry();
		geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
		geometry.setAttribute('normal', new THREE.BufferAttribute(normals, 3));
		geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));
		geometry.setAttribute('uv', new THREE.BufferAttribute(uvs, 2));

		geometry.setIndex([
			0, 1, 2,
			2, 1, 3,
		]);
		
		const textureLoader = new THREE.TextureLoader();
		const map = textureLoader.load('../examples/textures/uv_grid_opengl.jpg');

		const material = new THREE.MeshPhongMaterial({
			color: 0xffffff,
			vertexColors: true,
			map: map,
		});

		const box = new THREE.Mesh(geometry, material);
		this._scene.add(box);

		const helper = new VertexNormalsHelper(box, 0.1, 0xffff00);
		this._scene.add(helper);
	}

	resize() {
		const width = this._divContainer.clientWidth;
		const height = this._divContainer.clientHeight;

		this._camera.aspect = width / height;
		this._camera.updateProjectionMatrix();

		this._renderer.setSize(width, height);
	}

	render(time) {
		this._renderer.render(this._scene, this._camera);
		this.update(time);
		requestAnimationFrame(this.render.bind(this));
	}

	update(time) {
		time *= 0.001; // second unit
	}
}

window.onload = function() {
	new App();
}

```

#### light실습코드
```
import * as THREE from '../build/three.module.js';
import  {OrbitControls} from '../examples/jsm/controls/OrbitControls.js';
import  {RectAreaLightUniformsLib} from '../examples/jsm/lights/RectAreaLightUniformsLib.js';
import  {RectAreaLightHelper} from '../examples/jsm/helpers/RectAreaLightHelper.js';

class App {
    constructor() {
        const divContainer = document.querySelector('#webgl-container');
        this._divContainer = divContainer;

        const renderer = new THREE.WebGLRenderer({antialias: true});
        renderer.setPixelRatio(window.devicePixelRatio);
        divContainer.appendChild(renderer.domElement);
        this._renderer = renderer;

        const scene = new THREE.Scene();
        this._scene = scene;

        this._setupCamera();
        this._setupLight();
        this._setupModel();
        this._setupControls();

        window.onresize = this.resize.bind(this);
        this.resize();

        requestAnimationFrame(this.render.bind(this));
    }

    _setupControls() {
        new OrbitControls(this._camera, this._divContainer);
    }

    _setupCamera() {
        const width = this._divContainer.clientWidth;
        const height = this._divContainer.clientHeight;
        const camera = new THREE.PerspectiveCamera(75, width / height, 0.1, 100);
        //camera.position.z = 2;
        camera.position.set(7, 7, 0);
        camera.lookAt(0, 0, 0);
        this._camera = camera;
    }

    _setupLight() {

        RectAreaLightUniformsLib.init();
        const light = new THREE.RectAreaLight(0xffffff, 10, 6, 1);
        light.position.set(0, 5, 0);
        light.rotation.x = THREE.MathUtils.degToRad(-90);

        const helper = new RectAreaLightHelper(light);
        this._scene.add(helper);
        this._lightHelper = helper;

        this._scene.add(light);
        this._light = light;
    }

    _setupModel() {
        const groundGeometry = new THREE.PlaneGeometry(10, 10);
        const groundMaterial = new THREE.MeshStandardMaterial({
            color: '#2c3e50',
            roughness: 0.5,
            metalness: 0.5,
            side: THREE.DoubleSide,
        });

        const ground = new THREE.Mesh(groundGeometry, groundMaterial);
        ground.rotation.x = THREE.MathUtils.degToRad(-90);
        this._scene.add(ground);

        const bigSphereGeometry = new THREE.SphereGeometry(1.5, 64, 64, 0, Math.PI);
        const bigSphereMaterial = new THREE.MeshStandardMaterial({
            color: '#3498db',
            roughness: 0.1,
            metalness: 0.2,
        });

        const bigSphere = new THREE.Mesh(bigSphereGeometry, bigSphereMaterial);
        bigSphere.rotation.x = THREE.MathUtils.degToRad(-90);
        this._scene.add(bigSphere);

        const torusGeometry = new THREE.TorusGeometry(0.4, 0.1, 32, 32);
        const torusMaterial = new THREE.MeshStandardMaterial({
            color: '#27ae60',
            roughness: 0.5,
            metalness: 0.9,
        });

        for (let i = 0; i < 8; i++) {
            const torusPivot = new THREE.Object3D();
            const torus = new THREE.Mesh(torusGeometry, torusMaterial);
            torusPivot.rotation.y = THREE.MathUtils.degToRad(45 * i);
            torus.position.set(3, 0.5, 0);
            torusPivot.add(torus);
            this._scene.add(torusPivot);
        }

        const smallSphereGeometry = new THREE.SphereGeometry(0.3, 32, 32);
        const smallSphereMaterial = new THREE.MeshStandardMaterial({
            color: '#f39c12',
            roughness: 0.2,
            metalness: 0.5,
        });

        const smallSpherePivot = new THREE.Object3D();
        const smallSphere = new THREE.Mesh(smallSphereGeometry, smallSphereMaterial);
        smallSpherePivot.add(smallSphere)
    
        smallSpherePivot.name = 'smallSpherePivot';
        smallSphere.position.set(3, 0.5, 0);
        this._scene.add(smallSpherePivot);
    }

    resize() {
        const width = this._divContainer.clientWidth;
        const height = this._divContainer.clientHeight;

        this._camera.aspect = width / height;
        this._camera.updateProjectionMatrix();

        this._renderer.setSize(width, height);
    }

    render(time) {
        this._renderer.render(this._scene, this._camera);
        this.update(time);
        requestAnimationFrame(this.render.bind(this));
    }

    update(time) {
        time *= 0.001; // second unit

        const smallSpherePivot = this._scene.getObjectByName('smallSpherePivot');
        if (smallSpherePivot) {
            smallSpherePivot.rotation.y = THREE.MathUtils.degToRad(time * 50);

    
            if (this._light.target) {
                const smallSphere = smallSpherePivot.children[0];
              
                smallSphere.getWorldPosition(this._light.target.position);

                if (this._lightHelper) {
                    this._lightHelper.update();
                }
            }
        }

    }
}

window.onload = function() {
    new App();
}
```


### 소감 

이번에는 실습에서는 하나의 오브젝트로 끝나는 것이 아닌 여러 오브젝트를 사용하여 상호작용하게 함으로서 조금더 심도있는 실습이 될 수 있었고 영상을 보고 따라 하는 과정에서 막히는 부분이 생기면 챗지피티나 교수님꼐서 올려주신 자료를 통해 해결해 보면서 코드를 더 쉽게 짤 수 있었고 실제로 오브젝트가 코딩한데로 잘 작동되는 모습을 보고 코딩에 대한 자신감을 쌓을 수 있었습니다. 얼마 남지않은 동영상 강의를 잘 숙달하고 이번 수업이 끝나도 여러번 반복숙달을 하여 저만의 것으로 만들어 좋은 작품을 만들 수 있도록 해보겠습니다. 그래픽스 화이팅!!


