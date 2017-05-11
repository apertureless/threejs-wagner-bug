<template>
  <div ref="container"></div>
</template>

<script>
  import Wagner from '@superguigui/wagner'
  import VignettePass from '@superguigui/wagner/src/passes/vignette/VignettePass'

  import {
    Scene,
    PerspectiveCamera,
    WebGLRenderer,
    MeshBasicMaterial,
    PlaneGeometry,
    Mesh
  } from 'three'

  export default {
    props: {},
    data () {
      return {
        scene: new Scene(),
        camera: new PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 2000),
        renderer: new WebGLRenderer(),
        planeGeometry: null,
        floating: 0,
        composer: null,
        vignettePass: null
      }
    },
    mounted () {
      this.init()
      this.initPostprocessing()
      this.animate()
    },
    methods: {
      init () {
        this.camera.position.z = 4
        this.camera.rotation.x = 0.8

        const planeMaterial = new MeshBasicMaterial({
          color: 0xFF7801,
          wireframe: true
        })

        const segments = 15
        this.planeGeometry = new PlaneGeometry(30, 30, segments, segments)

        this.planeGeometry.computeFaceNormals()

        const plane = new Mesh(this.planeGeometry, planeMaterial)
        this.scene.add(plane)

        this.renderer.setSize(window.innerWidth, window.innerHeight)
        this.$refs.container.appendChild(this.renderer.domElement)
      },
      initPostprocessing () {
        this.composer = new Wagner.Composer(this.renderer)
        this.composer.setSize(window.innerWidth, window.innerHeight)
        this.vignettePass = new VignettePass({
          boost: 1.0,
          reduction: 1.5
        })
      },

      animate () {
        requestAnimationFrame(this.animate)
        this.render()
      },
      render () {
        this.composer.reset()
        this.composer.render(this.scene, this.camera)
        this.composer.pass(this.vignettePass)
        this.composer.toScreen()
        // this.renderer.render(this.scene, this.camera)
      }
    }
  }
</script>
