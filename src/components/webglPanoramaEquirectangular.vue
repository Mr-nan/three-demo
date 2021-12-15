<template>
    <div  id="container"></div>
</template>

<script>
    import * as Three from 'three'
    const img = require('../static/car1.jpg')
  
    export default {
        name: 'webglPanoramaCube',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                isUserInteracting: false,
                onPointerDownMouseX: 0,
                onPointerDownMouseY: 0,
                lon: 0,
                onPointerDownLon: 0,
                lat: 0,
                onPointerDownLat: 0,
                phi: 0,
                theta: 0

            };

        },
        mounted() {
            this.init()
            this.animate()
        },
        methods: {
            init() {
                let container = document.getElementById('container')
                this.scene = new Three.Scene()
                this.camera = new Three.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 1, 11000)

                const geometry = new Three.SphereGeometry(500, 60, 40)
                geometry.scale(-1, 1, 1);
                const texture = new Three.TextureLoader().load(img);
                const material = new Three.MeshBasicMaterial({
                    map: texture
                });

                const mesh = new Three.Mesh(geometry, material);

                this.scene.add(mesh);

                this.renderer = new Three.WebGLRenderer();
                this.renderer.setPixelRatio(window.devicePixelRatio);
                this.renderer.setSize(window.innerWidth, window.innerHeight);
                container.appendChild(this.renderer.domElement);

                container.style.touchAction = 'none';
                container.addEventListener('pointerdown', this.onPointerDown);
                // 滚轮时间
                document.addEventListener('wheel', this.onDocumentMouseWheel);

                document.addEventListener('dragover', function (event) {

                    event.preventDefault();
                    event.dataTransfer.dropEffect = 'copy';

                });

                document.addEventListener('dragenter', function () {

                    document.body.style.opacity = 0.5;

                });

                document.addEventListener('dragleave', function () {

                    document.body.style.opacity = 1;

                });

                document.addEventListener('drop', function (event) {
                    event.preventDefault();
                    const reader = new FileReader();
                    reader.addEventListener('load', function (event) {
                        material.map.image.src = event.target.result;
                        material.map.needsUpdate = true;

                    });
                    reader.readAsDataURL(event.dataTransfer.files[0]);
                    document.body.style.opacity = 1;

                });
                window.addEventListener('resize', this.onWindowResize);

            },


            onWindowResize() {

                this.camera.aspect = window.innerWidth / window.innerHeight;
                this.camera.updateProjectionMatrix();
                this.renderer.setSize(window.innerWidth, window.innerHeight);

            },
           
            onPointerDown(event) {

                if (event.isPrimary === false) return;

                this.isUserInteracting = true;

                this.onPointerDownMouseX = event.clientX;
                this.onPointerDownMouseY = event.clientY;

                this.onPointerDownLon = this.lon;
                this.onPointerDownLat = this.lat;

                document.addEventListener('pointermove', this.onPointerMove);
                document.addEventListener('pointerup', this.onPointerUp);

            },

            onPointerMove(event) {

                if (event.isPrimary === false) return;

                this.lon = (this.onPointerDownMouseX - event.clientX) * 0.1 + this.onPointerDownLon;
                this.lat = (event.clientY - this.onPointerDownMouseY) * 0.1 + this.onPointerDownLat;

            },

            onPointerUp() {

                if (event.isPrimary === false) return;

                this.isUserInteracting = false;

                document.removeEventListener('pointermove', this.onPointerMove);
                document.removeEventListener('pointerup', this.onPointerUp);

            },

            onDocumentMouseWheel(event) {

                const fov = this.camera.fov + event.deltaY * 0.05;
                this.camera.fov = Three.MathUtils.clamp(fov, 10, 75);
                this.camera.updateProjectionMatrix();

            },

            animate() {

                requestAnimationFrame(this.animate);
                
                this.update();

            },

            update() {

                if (this.isUserInteracting === false) {
                    // this.lon += 0.1;
                }

                this.lat = Math.max(-85, Math.min(85, this.lat));
                this.phi = Three.MathUtils.degToRad(90 - this.lat);
                this.theta = Three.MathUtils.degToRad(this.lon);

                const x = 500 * Math.sin(this.phi) * Math.cos(this.theta);
                const y = 500 * Math.cos(this.phi);
                const z = 500 * Math.sin(this.phi) * Math.sin(this.theta);

                this.camera.lookAt(x, y, z);
                this.renderer.render(this.scene, this.camera);

            }

        }

    }
</script>

<style lang="scss">

</style>