<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>3D Robot Mouse Tracker</title>
  <style>
    body { margin: 0; overflow: hidden; }
    canvas { display: block; }
  </style>
</head>
<body>
  <!-- Three.js va GLTFLoader -->
  <script src="https://cdn.jsdelivr.net/npm/three@0.155.0/build/three.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/three@0.155.0/examples/js/loaders/GLTFLoader.js"></script>

  <script>
    let scene, camera, renderer, robot;
    const mouse = new THREE.Vector2();

    init();
    animate();

    function init() {
      // Sahna
      scene = new THREE.Scene();
      scene.background = new THREE.Color(0xeeeeee);

      // Kamera
      camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
      camera.position.set(0, 1.5, 3);

      // Renderer
      renderer = new THREE.WebGLRenderer({antialias: true});
      renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(renderer.domElement);

      // Yorug‘lik
      const light = new THREE.DirectionalLight(0xffffff, 1);
      light.position.set(2, 2, 5);
      scene.add(light);

      const ambient = new THREE.AmbientLight(0x404040);
      scene.add(ambient);

      // Model yuklash
      const loader = new THREE.GLTFLoader();
      loader.load('robot.glb', function(gltf) {
        robot = gltf.scene;
        robot.scale.set(1, 1, 1);
        robot.position.set(0, 0, 0);
        scene.add(robot);
      });

      // Sichqoncha harakatini tinglash
      document.addEventListener('mousemove', onMouseMove, false);

      // Ekran o‘lchamiga moslash
      window.addEventListener('resize', () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
      });
    }

    function onMouseMove(event) {
      // Ekran koordinatalarini normalizatsiya qilamiz
      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    }

    function animate() {
      requestAnimationFrame(animate);

      if (robot) {
        // Sichqoncha joylashuvini 3D koordinataga aylantirish
        const vector = new THREE.Vector3(mouse.x, mouse.y, 0.5).unproject(camera);
        const dir = vector.sub(camera.position).normalize();
        const distance = (robot.position.z - camera.position.z) / dir.z;
        const pos = camera.position.clone().add(dir.multiplyScalar(distance));

        robot.lookAt(pos); // Robot sichqoncha tomonga qaraydi
      }

      renderer.render(scene, camera);
    }
  </script>
</body>
</html>
