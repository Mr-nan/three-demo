<template>
    <div id="container"></div>
</template>

<script>
    import * as Three from 'three'
    const img = require('../static/sun_temple_stripe.jpg')

    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
    export default {
        name: 'webglPanoramaCube',
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                mesh: null,
                controls: null
            };

        },
        mounted() {
            this.init()
            this.animate()
        },
        methods: {
            init() {
                let container = document.getElementById('container')
                this.renderer = new Three.WebGLRenderer()
                this.renderer.setPixelRatio(window.devicePixelRatio)
                this.renderer.setSize(window.innerWidth, window.innerHeight)

                container.appendChild(this.renderer.domElement)

                this.scene = new Three.Scene()
      
                this.camera = new Three.PerspectiveCamera(90, window.innerWidth/window.innerHeight, 0.1, 100)
                this.camera.position.z = 0.01

                this.controls = new OrbitControls(this.camera, this.renderer.domElement);
                this.controls.enableZoom = false;
                this.controls.enablePan = false;
                this.controls.enableDamping = true;
                this.controls.rotateSpeed = -0.25;

                const textures = this.getTexturesFromAtlasFile(img, 6);

                const materials = [];

                for (let i = 0; i < 6; i++) {
                    materials.push(new Three.MeshBasicMaterial({
                        map: textures[i]
                    }));

                }

                const skyBox = new Three.Mesh(new Three.BoxGeometry(1, 1, 1), materials);
                skyBox.geometry.scale(1, 1, -1);
                this.scene.add(skyBox);
                window.addEventListener('resize', this.onWindowResize);

            },
            getTexturesFromAtlasFile(atlasImgUrl, tilesNum) {
                const textures = [];
                for (let i = 0; i < tilesNum; i++) {
                    textures[i] = new Three.Texture();
                }

                new Three.ImageLoader()
                    .load(atlasImgUrl, (image) => {

                        let canvas, context;
                        const tileWidth = image.height;
                        for (let i = 0; i < textures.length; i++) {

                            canvas = document.createElement('canvas');
                            context = canvas.getContext('2d');
                            canvas.height = tileWidth;
							canvas.width = tileWidth;
                            context.drawImage(image, tileWidth * i, 0, tileWidth, tileWidth, 0, 0, tileWidth,tileWidth);
                            textures[i].image = canvas;
                            textures[i].needsUpdate = true;
                        }

                    });

                return textures;

            },

            onWindowResize() {

                this.camera.aspect = window.innerWidth / window.innerHeight;
                this.camera.updateProjectionMatrix();
                this.renderer.setSize(window.innerWidth, window.innerHeight);

            },
            animate() {

                requestAnimationFrame(this.animate);
                this.controls.update(); // required when damping is enabled
                this.renderer.render(this.scene, this.camera);

            }

        }

    }
</script>

<style lang="scss">

</style>