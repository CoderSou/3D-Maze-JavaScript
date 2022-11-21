
var KEY_CODE_LEFT_ARROW = 37;
var KEY_CODE_UP_ARROW = 38;
var KEY_CODE_RIGHT_ARROW = 39;
var KEY_CODE_DOWN_ARROW = 40;

var heading = Math.PI / 2; // Heading with respect to +ve x axis
var headingDelta = Math.PI/72; // 2.5 degrees
var eyeHeight = 1.5;

function init() {
    enablePicking();
    disableDefaultLight();
    var canvas = document.getElementById("canvas");
    canvas.addEventListener("keydown", keyPressed);
    canvas.focus();
}

function createScene() {

    var root = new osg.Node();
// WALLS
    var firstWall = makeWall(2, 6, 5, -3, -7, 2.501, 0.0, 0.0, 1.0);
    root.addChild(firstWall);
    var secondWall = makeWall(2, 4, 5, -5, 4, 2.501, 0.0, 1.0, 0.0);
    root.addChild(secondWall);
    var thirdWall = makeWall(6, 2, 5, -7, 1, 2.501, 1.0, 0.0, 0.0);
    root.addChild(thirdWall);
    var fourthWall = makeWall(2, 10, 5, 3, 1, 2.501, 0.1, 0.9, 0.9);
    root.addChild(fourthWall);
    var bottomWall = makeWall(20.0, 20.0, 0.01, 0.0, 0.0, 0.005, 0.5, 0.6, 0.2);
    root.addChild(bottomWall);
    var backWall = makeWall(20.0, 0.01, 6.0, 0.0, 10.005, 3.0, 0.3, 0.3, 0.0);
    root.addChild(backWall);
    var sideWall = makeWall(0.01, 20.01, 6.0, -10.0, 0.0, 3.0, 0.3, 0.3, 0.0);
    root.addChild(sideWall);


//Earth------
    var earthMatrixTransform = new osg.MatrixTransform();
    var earthSphere = osg.createTexturedSphere(2);
    //translate
    earthMatrixTransform = osg.Matrix.makeTranslate(-8, 4, 2.5, earthMatrixTransform);
    var earthTransform = new osg.MatrixTransform();
    earthTransform.setMatrix(earthMatrixTransform);
    //texture
    earthSphere.getOrCreateStateSet().setTextureAttributeAndModes(0, osg.Texture.createFromURL("res/textures/worldMap.jpg"));
    //rotate
    var updateCallback = new SimpleUpdateCallback();
    updateCallback.rateOfRotationRadsPerSecond = 0.45 * Math.PI;
    earthMatrixTransform.addUpdateCallback(updateCallback);

    root.addChild(earthTransform);
    earthTransform.addChild(earthMatrixTransform);
    earthMatrixTransform.addChild(earthSphere);
//--------


//Lights
    root.addChild(createLightTransform(-7.5, -6, 2, createPointLight(1.0, 0.0, 1.0, 1.0, 0.0, 0.0)));
    root.addChild(createLightTransform(-9, 7, 4, createPointLight(0.0, 1.0, 1.0, 1.0, 0.0, 0.0)));
    root.addChild(createLightTransform(-2, 2, 2, createSpotLight(3.0, 2.5, 1.0, 1.0, 1.0, 1.0, 1.0, 0.0, 0.0)));
    root.addChild(createDirectionalLight(1, 0, 0, 1.0, 1.0, 1.0));

    var vertexAttribArray = new osg.BufferArray(osg.BufferArray.ARRAY_BUFFER, null, 3);
    vertexAttribArray.setElements(new Float32Array(
        [-9.0, -10.0, 0.0,
            -7.0, -10.0, 0.0,
            -7.0, -10.0, 5.0,
            -9.0, -10.0, 5.0  ]));

    var colorAttribArray = new osg.BufferArray(osg.BufferArray.ARRAY_BUFFER, null, 4);
    colorAttribArray.setElements(new Float32Array(
        [    1, 0, 0, 1,
            0, 1, 0, 1,
            1, 0, 0, 1,
            0, 0, 1, 1]));

    var door = new osg.Geometry();
    door.setVertexAttribArray('Vertex', vertexAttribArray);
    door.setVertexAttribArray('Color', colorAttribArray);
    door.getPrimitives().push(new osg.DrawArrays(osg.PrimitiveSet.TRIANGLE_FAN,0, 4));

    door.onpick = function() {
        alert("To open door click OK!");
        vertexAttribArray.setElements(new Float32Array(
            [-9.0, -10.0, 0.0,
                -9.0, -8.0, 0.0,
                -9.0, -8.0, 5.0,
                -9.0, -10.0, 5.0  ]));

        door.getPrimitives().push(new osg.DrawArrays(osg.PrimitiveSet.TRIANGLE_FAN, 0, 4));
    }

    root.addChild(door);

    return root;
}

function makeWall(l, bt, h, x, y, z, r, g, b)
{
    var localRoot = new osg.Node();
    var cube = osg.createTexturedBox(0, 0, 0, l, bt, h);
    //translate
    var wallMatrix = new osg.Matrix.create();
    wallMatrix = osg.Matrix.makeTranslate(x, y, z, wallMatrix);
    var wallTransform = new osg.MatrixTransform();
    wallTransform.setMatrix(wallMatrix);
    //color , material and lighting
    var wallMaterial = new osg.Material();

    wallMaterial.setAmbient([r, g, b, 1.0]);
    wallMaterial.setDiffuse([r, g, b, 1.0]);
    wallMaterial.setSpecular([1.0, 1.0, 1.0, 1.0]);
    wallMaterial.setEmission([0.0, 0.0, 0.0, 1.0]);
    wallMaterial.setShininess(128);

    cube.getOrCreateStateSet().setAttributeAndMode(wallMaterial);

    localRoot.addChild(wallTransform);
    wallTransform.addChild(cube);
    return localRoot;
}

var SimpleUpdateCallback = function() {};

SimpleUpdateCallback.prototype = {

    rateOfRotationRadsPerSecond: 0.7 * Math.PI,

    update: function(node, nodeVisitor) {

        var currentTimeSeconds = nodeVisitor.getFrameStamp().getSimulationTime();

        var matrix = node.getMatrix();
        var angle = currentTimeSeconds * this.rateOfRotationRadsPerSecond;
        osg.Matrix.makeRotate(angle, 0.0, 0.0, 1.0, matrix);

        return true;
    }
};

var nextLightNumber = 0;
function getNextLightNumber() {
    return nextLightNumber++;
}

function createLightTransform(x, y, z, light) {
    var lightMatrix = new osg.Matrix.create();
    lightMatrix = osg.Matrix.makeTranslate(x, y, z, lightMatrix);
    var lightMatrixTransform = new osg.MatrixTransform();
    lightMatrixTransform.setMatrix(lightMatrix);

    lightMatrixTransform.addChild(light);
    return lightMatrixTransform;
}

function createSpotLight(x, y, z, r, g, b, c, l, q, cutoff, blend) {
    var lightNumber = getNextLightNumber();
    var spotLight = new osg.Light(lightNumber);

    spotLight.setPosition([0, 0, 0, 1]);

    spotLight.setDirection([x, y, z]);

    spotLight.setDiffuse([r, g, b, 1.0]);
    spotLight.setSpecular([r, g, b, 1.0]);
    spotLight.setAmbient([0.0, 0.0, 0.0, 1.0]);

    spotLight.setConstantAttenuation(c);
    spotLight.setLinearAttenuation(l);
    spotLight.setQuadraticAttenuation(q);

    if(typeof cutoff === 'undefined') cutoff = 45.0;
    spotLight.setSpotCutoff(cutoff);

    if(typeof blend === 'undefined') blend = 0.6;
    spotLight.setSpotBlend(blend);

    var lightSource = new osg.LightSource();
    lightSource.setLight(spotLight);
    return lightSource;
}

function createPointLight(r, g, b, c, l, q) {

    var lightNumber = getNextLightNumber();
    var pointLight = new osg.Light(lightNumber);
    pointLight.setPosition([0, 0, 0, 1]);

    pointLight.setDiffuse([r, g, b, 1.0]);
    pointLight.setSpecular([r, g, b, 1.0]);
    pointLight.setAmbient([0.0, 0.0, 0.0, 1.0]);

    pointLight.setConstantAttenuation(c);
    pointLight.setLinearAttenuation(l);
    pointLight.setQuadraticAttenuation(q);

    var lightSource = new osg.LightSource();
    lightSource.setLight(pointLight);

    return lightSource;
}

function createDirectionalLight(x, y, z, r, g, b) {

    var lightNumber = getNextLightNumber();
    var directionalLight = new osg.Light(lightNumber);

    directionalLight.setPosition([x, y, z, 0.0]);

    directionalLight.setDiffuse([r, g, b, 1.0]);
    directionalLight.setSpecular([r, g, b, 1.0]);
    directionalLight.setAmbient([0.0, 0.0, 0.0, 1.0]);

    var lightSource = new osg.LightSource();
    lightSource.setLight(directionalLight);
    return lightSource;
}

function keyPressed(event) {

    var keyCode = event.keyCode;

    var transX = 0;
    var transY = 0;

    if(keyCode == KEY_CODE_UP_ARROW) {
        transX = Math.cos(heading);
        transY = Math.sin(heading);
    }
    else if(keyCode == KEY_CODE_DOWN_ARROW) {
        transX = -Math.cos(heading);
        transY = -Math.sin(heading);
    }
    else if(keyCode == KEY_CODE_LEFT_ARROW) {
        heading += headingDelta;
    }
    else if(keyCode == KEY_CODE_RIGHT_ARROW) {
        heading -= headingDelta;
    }

    updateManipulator(transX, transY, heading);
}

function updateManipulator(targetTransX, targetTransY, viewDirection) {
    var manipulator = viewer.getManipulator();
    var currentTarget = osg.Vec3.create();
    manipulator.getTarget(currentTarget);

    var targetTranslation = osg.Vec3.createAndSet(targetTransX, targetTransY, 0.0);

    var newTarget = osg.Vec3.create();
    osg.Vec3.add(currentTarget, targetTranslation, newTarget);
    newTarget[2] = eyeHeight;
    manipulator.setTarget(newTarget);

    manipulator.setEyePosition([newTarget[0]-Math.cos(viewDirection),newTarget[1]-Math.sin(viewDirection),eyeHeight]);
}
