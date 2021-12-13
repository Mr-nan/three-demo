<template>
    <div>
        <div id="container"></div>
    </div>
</template>

<script>
    import * as Three from 'three'
    const img = require('../static/crate.gif')
    export default {

        name: 'HelloWorld',
       
        data() {
            return {
                camera: null,
                scene: null,
                renderer: null,
                mesh: null,

            }
        },
        methods: {
            init() {
                let container = document.getElementById('container')
                this.camera = new Three.PerspectiveCamera(70, window.innerWidth/window.innerHeight, 1,
                    1000)
                this.camera.position.z = 400
                this.scene = new Three.Scene()
                let texture = new Three.TextureLoader().load(img);
                let geometry = new Three.BoxGeometry(100, 100, 100)
                let material = new Three.MeshBasicMaterial({
                    map: texture
                })
                this.mesh = new Three.Mesh(geometry, material)
                this.scene.add(this.mesh)
                this.renderer = new Three.WebGLRenderer({
                    antialias: true
                })
                this.renderer.setPixelRatio( window.devicePixelRatio )
                this.renderer.setSize(window.innerWidth, window.innerHeight)
                container.appendChild(this.renderer.domElement)
                window.addEventListener('resize',this.onWindowResize)
            },
            onWindowResize() {
                this.camera.aspect = window.innerWidth / window.innerHeight;
                this.camera.updateProjectionMatrix();
                this.renderer.setSize(window.innerWidth, window.innerHeight);
            },

            animate() {
                requestAnimationFrame(this.animate)
                this.mesh.rotation.x += 0.01
                this.mesh.rotation.y += 0.01
                this.renderer.render(this.scene, this.camera)
            }
        },
        mounted() {
            this.init()
            this.animate()
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    #container {
        height: 400px;

    }
</style>