{ GO ON CODE NOT PREVIEW }
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="user-scalable=no">
    <title>clas3ic</title>
    <style>
        body {
            background: #131115;
        }
        #c1 {
            background: #fbf7fe;
        }
        #btn {
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #c1 {
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #main {
            transform-origin: 0px 0px;
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #config {
            position: fixed;
            left: 0px;
            top: 30px;
            display: none;
        }
    </style>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>

</body>
  <title>volumeshader_bm</title>
    <style>
        body {
            background: #131115;
        }
        #c1 {
            background: #fbf7fe;
        }
        #btn {
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #c1 {
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #main {
            transform-origin: 0px 0px;
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #config {
            position: fixed;
            left: 0px;
            top: 30px;
            display: none;
        }
    </style>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1920" height="1080">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var glcount;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 2.5;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
        var ceny = 0.0;
        var count = 0.0;
        var cenz = 0.0;
        var kcount = 3.0;
        var PI = 3.1415927;
    var vertshade;
    var fragshader;
        var shaderProgram;

        function getinter(a, da, b, db, t) {
            var u3 = (t - 0.5);
            u3 = (u3 * 1.5 - u3 * u3 * u3 * 2.0) + 0.5;
            return a + t * (da + db) * 0.5 + u3 * (b - a - 0.5 * (da + db)) + (0.25 - (t - 0.5) * (t - 0.5)) * (da - db) * 0.5;
        }
        function ontimer() {
            if (count < 8880) {
                ang2 = 0.6;
                ang1 = count / 600.0 * 2 * PI + PI;
                cenx = -0.5;
                ceny = 0.0;
                cenz = 0.0;
                len = 1.8 + 5.0 / (count / 900.0 + 1.0) - 6.0 * Math.exp(-(count / 450.0) * (count / 450.0 + 1.0));
                //ang2 -= 0.0002;
                var kc = count / 600.0;
                kc = 1.0 + kc - 0.8 * Math.tanh(kc / 0.8) + 14 * Math.exp(-kc * kc * 16);
                kcount = kc;
            }
            else if (count < 8880 + 200) {
                var u = (count - 8880) / 200.0;
                len = 2.26 - 0.26 * u;
                ang1 = getinter(0.6 * PI, PI * 2 / 3, 2 * PI - 3.14, 1.57 / 9.0, u);
                ang2 = getinter(0.6, 0.0, 0.0, 0.4 / 9.0, u);
                cenx = getinter(-0.5 + 2.26 * Math.cos(0.6 * PI) * Math.cos(0.6), -2.26 * Math.sin(0.6 * PI) * Math.cos(0.6) * PI * 2 / 3, -2.0, 3.4 / 9.0, u);
                ceny = getinter(2.26 * Math.sin(0.6), 0.0, 0.05, 1.5 / 9.0, u);
                cenz = getinter(2.26 * Math.sin(0.6 * PI) * Math.cos(0.6), 2.26 * Math.cos(0.6 * PI) * Math.cos(0.6) * PI * 2 / 3, -1.3, 1.3 / 9.0, u);
                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 13.0;
            }
            else if (count < 8880 + 200 + 3600) {
                var u = (count - 8880 - 200) / 1800.0 - 1.0;
                ang2 = 0.4 + 0.4 * u;
                ang1 = -1.57 + 1.57 * u;
                cenz = u * 1.3;
                ceny = 0.8 - 0.75 * u * u;
                cenx = -0.3 - 1.7 * u * u;
                len = 2.0;

                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 13.0;
            }
            else if (count < 8880 + 200 + 3600 + 300) {
                var u = (count - 8880 - 200 - 3600) / 300.0;
                var u1 = 1.0 + Math.sin(PI * u) / 6.0 / PI;
                var u2 = Math.tanh((u1 - 1.0) * 100) / 100 + 1.0;
                var u3 = (u - 0.5);
                u3 = (u3 * 1.5 - u3 * u3 * u3 * 2.0) + 0.5;
                u3 = u3 + (0.25 - (u - 0.5) * (u - 0.5)) / 12.0;
                ang2 = 0.8 - 0.8 * u;
                ang1 = PI * u3;
                cenz = u1 * 1.3;
                ceny = 0.8 - 0.75 * u2 * u2;
                cenx = -0.3 - 1.7 * u1 * u1;
                len = 2.0;

                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 15.0;
            }
            else if (count < 8880 + 200 + 3600 + 300 + 3600) {
                var u = (count - 8880 - 200 - 3600 - 300) / 1800.0 - 1.0;
                ang2 = 0.4 + 0.4 * u;
                ang1 = +PI / 2 - PI / 2 * u + (u > 0.5 ? (1.0 - Math.cos((u - 0.5) * PI)) * PI / 2 : 0.0);
                cenz = -u * 1.3 + (u > 0.5 ? (u - 0.5) * (u - 0.5) * 1.5 : 0.0);
                ceny = 0.8 - 0.75 * u * u + (u > 0.5 ? (u - 0.5) * (u - 0.5) * 3 : 0.0);
                cenx = -0.3 - 1.7 * u * u;
                len = 2.0;

                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 19 - (u + 1.0 / 3.0) * (u + 1.0 / 3.0) * 9;
            }
            else {
                var u = (count - 8880 - 200 - 3600 - 300 - 3600) / 400.0;
                len = 2.0 + u * 4.8;
                ang1 = getinter(0.5 * PI, (PI * PI / 2 - PI / 2) / 4.5, PI, 0.0, u);
                ang2 = getinter(0.8, 0.4 / 4.5, 0.6, 0.0, u);
                cenx = getinter(-2.0, -3.4 / 4.5, -0.5 + 6.8 * Math.cos(PI) * Math.cos(0.6), 0.0, u);
                ceny = getinter(0.8, 1.5 / 4.5, 6.8 * Math.sin(0.6), 0.0, u);
                cenz = getinter(-1.3 + 1.5 / 4, 0.2 / 4.5, 6.8 * Math.sin(PI) * Math.cos(0.6), 0.0, u);
                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 1.0 + (u - 1.0) * (u - 1.0) * 2.0;
            }
            t2 = (new Date()).getTime();
            if (t2 - t1 < 2000) {
                count += 60.0 / 1000.0 * (t2 - t1);
            }
            while (count > 8880 + 200 + 3600 + 300 + 3600 + 400) {
                count -= 8880 + 200 + 3600 + 300 + 3600 + 400;
            }
            t1 = t2;
            draw();
            window.requestAnimationFrame(ontimer);
        }
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
	    gl.uniform1f(glcount, kcount - Math.sin(kcount * 2.0 * 3.1415926) / 2.0 / 3.1415926);
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx*9>cy*16){
            document.getElementById("main").style.left = cx / 2 - cy * 16 / 9 / 2 + "px";
            document.getElementById("main").style.top = 0 + "px";
            cx=cy*16/9;
        }
        else{
            document.getElementById("main").style.left = 0 + "px";
            document.getElementById("main").style.top = cy / 2 - cx * 9 / 16 / 2 + "px";
            cy = cx * 9 / 16;
            cy=cx*9/16;
        }
        document.getElementById("main").style.width=1920+"px";
        document.getElementById("main").style.height=1080+"px";
        document.getElementById("main").style.transform="scale("+cx/1920+","+cy/1080+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if (cx * 9 > cy * 16) {
            document.getElementById("main").style.left = cx / 2 - cy * 16 / 9 / 2 + "px";
            document.getElementById("main").style.top = 0 + "px";
            cx = cy * 16 / 9;
        }
        else {
            document.getElementById("main").style.left = 0 + "px";
            document.getElementById("main").style.top = cy / 2 - cx * 9 / 16 / 2 + "px";
            cy = cx * 9 / 16;
        }
        document.getElementById("main").style.width=1920+"px";
        document.getElementById("main").style.height=1080+"px";
        document.getElementById("main").style.transform="scale("+cx/1920+","+cy/1080+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len,count;\n" +
            "vec3 ver;\n" +
            "int sign;" +
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "float kernal(vec3 ver){\n" +
            "   float a,b,c,a1,b1,c1;\n" +
            "   float j;\n" +
            "   a=ver.z;\n" +
            "   b=0.0;\n" +
            "   for(int i=0;i<20;i++){\n" +
            "       c=a*a-b*b+ver.x;\n" +
            "       b=2.0*a*b+ver.y;\n" +
            "       a=c;\n" +
            "       if(a*a+b*b>1024.0){\n" +
            "          return 4.0-(a*a+b*b);\n" +
            "       }\n" +
            "       if(float(i)>count-2.0){j=float(i)+1.0; break;}\n"+
            "   }" +
            "   a1=a*a-b*b+ver.x;\n" +
            "   b1=2.0*a*b+ver.y;\n" +
            "   a=(count-j)*a1+(1.0-count+j)*a;\n" +
            "   b=(count-j)*b1+(1.0-count+j)*b;\n" +
            "   return 4.0-(a*a+b*b);" +
            "}\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;" +
            "   v1 = kernal(origin + dir * (step*len)*3.0);\n" +
            "   v2 = kernal(origin+ dir * (step*len)*2.0);\n" +
            "   for (int k = 4; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v >= 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len*3.0) {\n" +
            "               sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len*3.0) {\n" +
            "               sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
            "      n.x = kernal(ver - right * (r3*0.001)) - kernal(ver + right * (r3*0.001));\n" +
            "      n.y = kernal(ver - up * (r3*0.001)) - kernal(ver + up * (r3*0.001));\n" +
            "      n.z = kernal(ver + forward * (r3*0.001)) - kernal(ver - forward * (r3*0.001));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
            "      n.x = 0.0;\n" +
            "      n.y = 1.0;\n" +
            "      n.z = 1.0;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        glcount = gl.getUniformLocation(shaderProgram, 'count');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);
        gl.viewport(0, 0, 1920, 1080);
        window.requestAnimationFrame(ontimer);
    }
    </script>
    </body>
  <title>volumeshader_bm</title>
    <style>
        body {
            background: #131115;
        }
        #c1 {
            background: #fbf7fe;
        }
        #btn {
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #c1 {
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #main {
            transform-origin: 0px 0px;
            position: fixed;
            left: 0px;
            top: 0px;
        }
        #config {
            position: fixed;
            left: 0px;
            top: 30px;
            display: none;
        }
    </style>
    </body>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1920" height="1080">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var glcount;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 2.5;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
        var ceny = 0.0;
        var count = 0.0;
        var cenz = 0.0;
        var kcount = 3.0;
        var PI = 3.1415927;
    var vertshade;
    var fragshader;
        var shaderProgram;

        function getinter(a, da, b, db, t) {
            var u3 = (t - 0.5);
            u3 = (u3 * 1.5 - u3 * u3 * u3 * 2.0) + 0.5;
            return a + t * (da + db) * 0.5 + u3 * (b - a - 0.5 * (da + db)) + (0.25 - (t - 0.5) * (t - 0.5)) * (da - db) * 0.5;
        }
        function ontimer() {
            if (count < 8880) {
                ang2 = 0.6;
                ang1 = count / 600.0 * 2 * PI + PI;
                cenx = -0.5;
                ceny = 0.0;
                cenz = 0.0;
                len = 1.8 + 5.0 / (count / 900.0 + 1.0) - 6.0 * Math.exp(-(count / 450.0) * (count / 450.0 + 1.0));
                //ang2 -= 0.0002;
                var kc = count / 600.0;
                kc = 1.0 + kc - 0.8 * Math.tanh(kc / 0.8) + 14 * Math.exp(-kc * kc * 16);
                kcount = kc;
            }
            else if (count < 8880 + 200) {
                var u = (count - 8880) / 200.0;
                len = 2.26 - 0.26 * u;
                ang1 = getinter(0.6 * PI, PI * 2 / 3, 2 * PI - 3.14, 1.57 / 9.0, u);
                ang2 = getinter(0.6, 0.0, 0.0, 0.4 / 9.0, u);
                cenx = getinter(-0.5 + 2.26 * Math.cos(0.6 * PI) * Math.cos(0.6), -2.26 * Math.sin(0.6 * PI) * Math.cos(0.6) * PI * 2 / 3, -2.0, 3.4 / 9.0, u);
                ceny = getinter(2.26 * Math.sin(0.6), 0.0, 0.05, 1.5 / 9.0, u);
                cenz = getinter(2.26 * Math.sin(0.6 * PI) * Math.cos(0.6), 2.26 * Math.cos(0.6 * PI) * Math.cos(0.6) * PI * 2 / 3, -1.3, 1.3 / 9.0, u);
                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 13.0;
            }
            else if (count < 8880 + 200 + 3600) {
                var u = (count - 8880 - 200) / 1800.0 - 1.0;
                ang2 = 0.4 + 0.4 * u;
                ang1 = -1.57 + 1.57 * u;
                cenz = u * 1.3;
                ceny = 0.8 - 0.75 * u * u;
                cenx = -0.3 - 1.7 * u * u;
                len = 2.0;

                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 13.0;
            }
            else if (count < 8880 + 200 + 3600 + 300) {
                var u = (count - 8880 - 200 - 3600) / 300.0;
                var u1 = 1.0 + Math.sin(PI * u) / 6.0 / PI;
                var u2 = Math.tanh((u1 - 1.0) * 100) / 100 + 1.0;
                var u3 = (u - 0.5);
                u3 = (u3 * 1.5 - u3 * u3 * u3 * 2.0) + 0.5;
                u3 = u3 + (0.25 - (u - 0.5) * (u - 0.5)) / 12.0;
                ang2 = 0.8 - 0.8 * u;
                ang1 = PI * u3;
                cenz = u1 * 1.3;
                ceny = 0.8 - 0.75 * u2 * u2;
                cenx = -0.3 - 1.7 * u1 * u1;
                len = 2.0;

                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 15.0;
            }
            else if (count < 8880 + 200 + 3600 + 300 + 3600) {
                var u = (count - 8880 - 200 - 3600 - 300) / 1800.0 - 1.0;
                ang2 = 0.4 + 0.4 * u;
                ang1 = +PI / 2 - PI / 2 * u + (u > 0.5 ? (1.0 - Math.cos((u - 0.5) * PI)) * PI / 2 : 0.0);
                cenz = -u * 1.3 + (u > 0.5 ? (u - 0.5) * (u - 0.5) * 1.5 : 0.0);
                ceny = 0.8 - 0.75 * u * u + (u > 0.5 ? (u - 0.5) * (u - 0.5) * 3 : 0.0);
                cenx = -0.3 - 1.7 * u * u;
                len = 2.0;

                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 19 - (u + 1.0 / 3.0) * (u + 1.0 / 3.0) * 9;
            }
            else {
                var u = (count - 8880 - 200 - 3600 - 300 - 3600) / 400.0;
                len = 2.0 + u * 4.8;
                ang1 = getinter(0.5 * PI, (PI * PI / 2 - PI / 2) / 4.5, PI, 0.0, u);
                ang2 = getinter(0.8, 0.4 / 4.5, 0.6, 0.0, u);
                cenx = getinter(-2.0, -3.4 / 4.5, -0.5 + 6.8 * Math.cos(PI) * Math.cos(0.6), 0.0, u);
                ceny = getinter(0.8, 1.5 / 4.5, 6.8 * Math.sin(0.6), 0.0, u);
                cenz = getinter(-1.3 + 1.5 / 4, 0.2 / 4.5, 6.8 * Math.sin(PI) * Math.cos(0.6), 0.0, u);
                cenx -= len * Math.cos(ang1) * Math.cos(ang2);
                ceny -= len * Math.sin(ang2);
                cenz -= len * Math.sin(ang1) * Math.cos(ang2);
                kcount = 1.0 + (u - 1.0) * (u - 1.0) * 2.0;
            }
            t2 = (new Date()).getTime();
            if (t2 - t1 < 2000) {
                count += 60.0 / 1000.0 * (t2 - t1);
            }
            while (count > 8880 + 200 + 3600 + 300 + 3600 + 400) {
                count -= 8880 + 200 + 3600 + 300 + 3600 + 400;
            }
            t1 = t2;
            draw();
            window.requestAnimationFrame(ontimer);
        }
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
	    gl.uniform1f(glcount, kcount - Math.sin(kcount * 2.0 * 3.1415926) / 2.0 / 3.1415926);
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx*9>cy*16){
            document.getElementById("main").style.left = cx / 2 - cy * 16 / 9 / 2 + "px";
            document.getElementById("main").style.top = 0 + "px";
            cx=cy*16/9;
        }
        else{
            document.getElementById("main").style.left = 0 + "px";
            document.getElementById("main").style.top = cy / 2 - cx * 9 / 16 / 2 + "px";
            cy = cx * 9 / 16;
            cy=cx*9/16;
        }
        document.getElementById("main").style.width=1920+"px";
        document.getElementById("main").style.height=1080+"px";
        document.getElementById("main").style.transform="scale("+cx/1920+","+cy/1080+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if (cx * 9 > cy * 16) {
            document.getElementById("main").style.left = cx / 2 - cy * 16 / 9 / 2 + "px";
            document.getElementById("main").style.top = 0 + "px";
            cx = cy * 16 / 9;
        }
        else {
            document.getElementById("main").style.left = 0 + "px";
            document.getElementById("main").style.top = cy / 2 - cx * 9 / 16 / 2 + "px";
            cy = cx * 9 / 16;
        }
        document.getElementById("main").style.width=1920+"px";
        document.getElementById("main").style.height=1080+"px";
        document.getElementById("main").style.transform="scale("+cx/1920+","+cy/1080+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len,count;\n" +
            "vec3 ver;\n" +
            "int sign;" +
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "float kernal(vec3 ver){\n" +
            "   float a,b,c,a1,b1,c1;\n" +
            "   float j;\n" +
            "   a=ver.z;\n" +
            "   b=0.0;\n" +
            "   for(int i=0;i<20;i++){\n" +
            "       c=a*a-b*b+ver.x;\n" +
            "       b=2.0*a*b+ver.y;\n" +
            "       a=c;\n" +
            "       if(a*a+b*b>1024.0){\n" +
            "          return 4.0-(a*a+b*b);\n" +
            "       }\n" +
            "       if(float(i)>count-2.0){j=float(i)+1.0; break;}\n"+
            "   }" +
            "   a1=a*a-b*b+ver.x;\n" +
            "   b1=2.0*a*b+ver.y;\n" +
            "   a=(count-j)*a1+(1.0-count+j)*a;\n" +
            "   b=(count-j)*b1+(1.0-count+j)*b;\n" +
            "   return 4.0-(a*a+b*b);" +
            "}\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;" +
            "   v1 = kernal(origin + dir * (step*len)*3.0);\n" +
            "   v2 = kernal(origin+ dir * (step*len)*2.0);\n" +
            "   for (int k = 4; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v >= 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len*3.0) {\n" +
            "               sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len*3.0) {\n" +
            "               sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
            "      n.x = kernal(ver - right * (r3*0.001)) - kernal(ver + right * (r3*0.001));\n" +
            "      n.y = kernal(ver - up * (r3*0.001)) - kernal(ver + up * (r3*0.001));\n" +
            "      n.z = kernal(ver + forward * (r3*0.001)) - kernal(ver - forward * (r3*0.001));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
            "      n.x = 0.0;\n" +
            "      n.y = 1.0;\n" +
            "      n.z = 1.0;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        glcount = gl.getUniformLocation(shaderProgram, 'count');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);
        gl.viewport(0, 0, 1920, 1080);
        window.requestAnimationFrame(ontimer);
    }
    </script>
    </head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</body>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</body>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
</head>
<body style="overflow-x:hidden;overflow-y:hidden">
<div id="main">
    <canvas id="c1" width="1024" height="1024">
        <span>不支持canvas浏览器</span>
    </canvas>
</div>
    <button id="btn">CONFIG</button>
    <div id="config">
        <textarea name="" id="kernel" cols="30" rows="10"></textarea>
        <br />
        <button id="apply">APPLY</button>
        <button id="cancle">CANCLE</button>
    </div>
    <script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}";
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
<script>
    var cx, cy;
    var glposition;
    var glright;
    var glforward;
    var glup;
    var glorigin;
    var glx;
    var gly;
    var gllen;
    var canvas;
    var gl;
    var date = new Date();
    var md = 0,mx,my;
    var t2,t1 = date.getTime();
    var mx = 0, my = 0, mx1 = 0, my1 = 0, lasttimen = 0;
    var ml = 0, mr = 0, mm = 0;
    var len = 1.6;
    var ang1 = 2.8;
    var ang2 = 0.4;
    var cenx = 0.0;
    var ceny = 0.0;
    var cenz = 0.0;
        var KERNEL = "float kernal(vec3 ver){\n" +
            "   vec3 a;\n" +
            "float b,c,d,e;\n" +
            "   a=ver;\n" +
            "   for(int i=0;i<5;i++){\n" +
            "       b=length(a);\n" +
            "       c=atan(a.y,a.x)*8.0;\n" +
            "       e=1.0/b;\n" +
            "       d=acos(a.z/b)*8.0;\n" +
            "       b=pow(b,8.0);\n" +
            "       a=vec3(b*sin(d)*cos(c),b*sin(d)*sin(c),b*cos(d))+ver;\n" +
            "       if(b>6.0){\n" +
            "           break;\n" +
            "       }\n" +
            "   }" +
            "   return 4.0-a.x*a.x-a.y*a.y-a.z*a.z;" +
            "}"
            ;
    var vertshade;
    var fragshader;
    var shaderProgram;
    function ontimer() {
        ang1+=0.01;
        draw();
        window.requestAnimationFrame(ontimer);
    }
    document.addEventListener("mousedown",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 1;
                mm = 0;
            }
            if (oEvent.button == 2) {
                mr = 1;
                mm = 0;
            }
            mx = oEvent.clientX;
            my = oEvent.clientY;
        },
        false);
    document.addEventListener("mouseup",
        function (ev) {
            var oEvent = ev || event;
            if (oEvent.button == 0) {
                ml = 0;
            }
            if (oEvent.button == 2) {
                mr = 0;
            }
        },
        false);
    document.addEventListener("mousemove",
        function (ev) {
        var oEvent = ev || event;
        if (ml == 1) {
            ang1 += (oEvent.clientX - mx) * 0.002;
            ang2 += (oEvent.clientY - my) * 0.002;
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        if (mr == 1) {
            var l = len * 4.0 / (cx + cy);
            cenx += l * (-(oEvent.clientX - mx) * Math.sin(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.cos(ang1));
            ceny += l * ((oEvent.clientY - my) * Math.cos(ang2));
            cenz += l * ((oEvent.clientX - mx) * Math.cos(ang1) - (oEvent.clientY - my) * Math.sin(ang2) * Math.sin(ang1));
            if (oEvent.clientX != mx || oEvent.clientY != my) {
                mm = 1;
            }
        }
        mx = oEvent.clientX;
        my = oEvent.clientY;
        },
        false);
    document.addEventListener("mousewheel",
        function (ev) {
            ev.preventDefault();
            var oEvent = ev || event;
            len *= Math.exp(-0.001 * oEvent.wheelDelta);
        },
        false);
    document.addEventListener("touchstart",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchend",
        function (ev) {
            var n = ev.touches.length;
            if (n == 1) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                mx = oEvent.clientX;
                my = oEvent.clientY;
                oEvent = ev.touches[1];
                mx1 = oEvent.clientX;
                my1 = oEvent.clientY;
            }
            lasttimen = n;
        },
        false);
    document.addEventListener("touchmove",
        function (ev) {
            ev.preventDefault();
            var n = ev.touches.length;
            if (n == 1&&lasttimen==1) {
                var oEvent = ev.touches[0];
                ang1 += (oEvent.clientX - mx) * 0.002;
                ang2 += (oEvent.clientY - my) * 0.002;
                mx = oEvent.clientX;
                my = oEvent.clientY;
            }
            else if (n == 2) {
                var oEvent = ev.touches[0];
                var oEvent1 = ev.touches[1];
                var l = len * 2.0 / (cx + cy), l1;
                cenx += l * (-(oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.sin(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.cos(ang1));
                ceny += l * ((oEvent.clientY + oEvent1.clientY - my - my1) * Math.cos(ang2));
                cenz += l * ((oEvent.clientX + oEvent1.clientX - mx - mx1) * Math.cos(ang1) - (oEvent.clientY + oEvent1.clientY - my - my1) * Math.sin(ang2) * Math.sin(ang1));
                l1 = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1)+1.0);
                mx = oEvent.clientX;
                my = oEvent.clientY;
                mx1 = oEvent1.clientX;
                my1 = oEvent1.clientY;
                l = Math.sqrt((mx - mx1) * (mx - mx1) + (my - my1) * (my - my1) + 1.0);
                len *= l1 / l;
            }
            lasttimen = n;
        },
        false);
    document.oncontextmenu = function (event) {
        if (mm == 1) {
            event.preventDefault();
        }
    };
    function draw() {
        date = new Date();
        var t2 = date.getTime();
        //ang1 += (t2 - t1)*0.001;
        t1 = t2;
        //ang1 += 0.1;
        gl.uniform1f(glx, cx * 2.0 / (cx + cy));
        gl.uniform1f(gly, cy * 2.0 / (cx + cy));
        gl.uniform1f(gllen, len);
        gl.uniform3f(glorigin, len * Math.cos(ang1) * Math.cos(ang2) + cenx, len * Math.sin(ang2) + ceny, len * Math.sin(ang1) * Math.cos(ang2) + cenz);
        gl.uniform3f(glright, Math.sin(ang1), 0, -Math.cos(ang1));
        gl.uniform3f(glup, -Math.sin(ang2) * Math.cos(ang1), Math.cos(ang2), -Math.sin(ang2) * Math.sin(ang1));
        gl.uniform3f(glforward, -Math.cos(ang1) * Math.cos(ang2), -Math.sin(ang2), -Math.sin(ang1) * Math.cos(ang2));
        gl.drawArrays(gl.TRIANGLES, 0, 6);
        gl.finish();
    }
    window.onresize = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
    }
    window.onload = function () {
        cx = document.body.clientWidth;
        cy = document.body.clientHeight;
        if(cx>cy){
            cx=cy;
        }
        else{
            cy=cx;
        }
        document.getElementById("main").style.width=1024+"px";
        document.getElementById("main").style.height=1024+"px";
        document.getElementById("main").style.transform="scale("+cx/1024+","+cy/1024+")";
        var positions = [-1.0, -1.0, 0.0, 1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, -1.0, 0.0, 1.0, 1.0, 0.0, -1.0, 1.0, 0.0];
        var VSHADER_SOURCE =
            "#version 100 \n"+
            "precision highp float;\n" +
            "attribute vec4 position;" +
            "varying vec3 dir, localdir;" +
            "uniform vec3 right, forward, up, origin;" +
            "uniform float x,y;" +
            "void main() {" +
            "   gl_Position = position; " +
            "   dir = forward + right * position.x*x + up * position.y*y;" +
            "   localdir.x = position.x*x;" +
            "   localdir.y = position.y*y;" +
            "   localdir.z = -1.0;" +
            "} ";
        var FSHADER_SOURCE =
            "#version 100 \n" +
            "#define PI 3.14159265358979324\n" +
            "#define M_L 0.3819660113\n" +
            "#define M_R 0.6180339887\n" +
            "#define MAXR 8\n" +
            "#define SOLVER 8\n" +
            "precision highp float;\n" +
            "float kernal(vec3 ver)\n;" +
            "uniform vec3 right, forward, up, origin;\n" +
            "varying vec3 dir, localdir;\n" +
            "uniform float len;\n" +
            "vec3 ver;\n" +
            "int sign;"+
            "float v, v1, v2;\n" +
            "float r1, r2, r3, r4, m1, m2, m3, m4;\n" +
            "vec3 n, reflect;\n" +
            "const float step = 0.002;\n" +
            "vec3 color;\n" +
            "void main() {\n" +
            "   color.r=0.0;\n" +
            "   color.g=0.0;\n" +
            "   color.b=0.0;\n" +
            "   sign=0;"+
            "   v1 = kernal(origin + dir * (step*len));\n" +
            "   v2 = kernal(origin);\n" +
            "   for (int k = 2; k < 1002; k++) {\n" +
            "      ver = origin + dir * (step*len*float(k));\n" +
            "      v = kernal(ver);\n" +
            "      if (v > 0.0 && v1 < 0.0) {\n" +
            "         r1 = step * len*float(k - 1);\n" +
            "         r2 = step * len*float(k);\n" +
            "         m1 = kernal(origin + dir * r1);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         for (int l = 0; l < SOLVER; l++) {\n" +
            "            r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "            m3 = kernal(origin + dir * r3);\n" +
            "            if (m3 > 0.0) {\n" +
            "               r2 = r3;\n" +
            "               m2 = m3;\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r3;\n" +
            "               m1 = m3;\n" +
            "            }\n" +
            "         }\n" +
            "         if (r3 < 2.0 * len) {\n" +
            "               sign=1;" +
            "            break;\n" +
            "         }\n" +
            "      }\n" +
            "      if (v < v1&&v1>v2&&v1 < 0.0 && (v1*2.0 > v || v1 * 2.0 > v2)) {\n" +
            "         r1 = step * len*float(k - 2);\n" +
            "         r2 = step * len*(float(k) - 2.0 + 2.0*M_L);\n" +
            "         r3 = step * len*(float(k) - 2.0 + 2.0*M_R);\n" +
            "         r4 = step * len*float(k);\n" +
            "         m2 = kernal(origin + dir * r2);\n" +
            "         m3 = kernal(origin + dir * r3);\n" +
            "         for (int l = 0; l < MAXR; l++) {\n" +
            "            if (m2 > m3) {\n" +
            "               r4 = r3;\n" +
            "               r3 = r2;\n" +
            "               r2 = r4 * M_L + r1 * M_R;\n" +
            "               m3 = m2;\n" +
            "               m2 = kernal(origin + dir * r2);\n" +
            "            }\n" +
            "            else {\n" +
            "               r1 = r2;\n" +
            "               r2 = r3;\n" +
            "               r3 = r4 * M_R + r1 * M_L;\n" +
            "               m2 = m3;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "            }\n" +
            "         }\n" +
            "         if (m2 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r2;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "         else if (m3 > 0.0) {\n" +
            "            r1 = step * len*float(k - 2);\n" +
            "            r2 = r3;\n" +
            "            m1 = kernal(origin + dir * r1);\n" +
            "            m2 = kernal(origin + dir * r2);\n" +
            "            for (int l = 0; l < SOLVER; l++) {\n" +
            "               r3 = r1 * 0.5 + r2 * 0.5;\n" +
            "               m3 = kernal(origin + dir * r3);\n" +
            "               if (m3 > 0.0) {\n" +
            "                  r2 = r3;\n" +
            "                  m2 = m3;\n" +
            "               }\n" +
            "               else {\n" +
            "                  r1 = r3;\n" +
            "                  m1 = m3;\n" +
            "               }\n" +
            "            }\n" +
            "            if (r3 < 2.0 * len&&r3> step*len) {\n" +
            "                   sign=1;" +
            "               break;\n" +
            "            }\n" +
            "         }\n" +
            "      }\n" +
            "      v2 = v1;\n" +
            "      v1 = v;\n" +
            "   }\n" +
            "   if (sign==1) {\n" +
            "      ver = origin + dir*r3 ;\n" +
                "       r1=ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;" +
            "      n.x = kernal(ver - right * (r3*0.00025)) - kernal(ver + right * (r3*0.00025));\n" +
            "      n.y = kernal(ver - up * (r3*0.00025)) - kernal(ver + up * (r3*0.00025));\n" +
            "      n.z = kernal(ver + forward * (r3*0.00025)) - kernal(ver - forward * (r3*0.00025));\n" +
            "      r3 = n.x*n.x+n.y*n.y+n.z*n.z;\n" +
            "      n = n * (1.0 / sqrt(r3));\n" +
            "      ver = localdir;\n" +
            "      r3 = ver.x*ver.x+ver.y*ver.y+ver.z*ver.z;\n" +
            "      ver = ver * (1.0 / sqrt(r3));\n" +
            "      reflect = n * (-2.0*dot(ver, n)) + ver;\n" +
            "      r3 = reflect.x*0.276+reflect.y*0.920+reflect.z*0.276;\n" +
            "      r4 = n.x*0.276+n.y*0.920+n.z*0.276;\n" +
            "      r3 = max(0.0,r3);\n" +
            "      r3 = r3 * r3*r3*r3;\n" +
            "      r3 = r3 * 0.45 + r4 * 0.25 + 0.3;\n" +
                "      n.x = sin(r1*10.0)*0.5+0.5;\n" +
                "      n.y = sin(r1*10.0+2.05)*0.5+0.5;\n" +
                "      n.z = sin(r1*10.0-2.05)*0.5+0.5;\n" +
            "      color = n*r3;\n" +
            "   }\n" +
            "   gl_FragColor = vec4(color.x, color.y, color.z, 1.0);" +
            "}";
        canvas = document.getElementById('c1');
        gl = canvas.getContext('webgl');
        vertshader = gl.createShader(gl.VERTEX_SHADER);
        fragshader = gl.createShader(gl.FRAGMENT_SHADER);
        shaderProgram = gl.createProgram();
        gl.shaderSource(vertshader, VSHADER_SOURCE);
        gl.compileShader(vertshader);
        var infov = gl.getShaderInfoLog(vertshader);
        gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
        gl.compileShader(fragshader);
        var infof = gl.getShaderInfoLog(fragshader);
        gl.attachShader(shaderProgram, vertshader);
        gl.attachShader(shaderProgram, fragshader);
        gl.linkProgram(shaderProgram);
        gl.useProgram(shaderProgram);
        if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
            var info = gl.getProgramInfoLog(shaderProgram);
            throw 'Could not compile WebGL program. \n\n' + infov + infof + info;
        }
        glposition = gl.getAttribLocation(shaderProgram, 'position');
        glright = gl.getUniformLocation(shaderProgram, 'right');
        glforward = gl.getUniformLocation(shaderProgram, 'forward');
        glup = gl.getUniformLocation(shaderProgram, 'up');
        glorigin = gl.getUniformLocation(shaderProgram, 'origin');
        glx = gl.getUniformLocation(shaderProgram, 'x');
        gly = gl.getUniformLocation(shaderProgram, 'y');
        gllen = gl.getUniformLocation(shaderProgram, 'len');
        var buffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, buffer);
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        gl.vertexAttribPointer(glposition, 3, gl.FLOAT, false, 0, 0);
        gl.enableVertexAttribArray(glposition);

        gl.viewport(0, 0, 1024, 1024);
        draw();
        window.requestAnimationFrame(ontimer);
        document.getElementById("kernel").value = KERNEL;
        document.getElementById("btn").addEventListener("click", function() {
            var state = this.innerText == "CONFIG";
            this.innerText = state ? "HIDE" : "CONFIG";
            document.getElementById("config").style.display = state ? "inline" : "none";
        });
        document.getElementById("apply").addEventListener("click", function() {
            KERNEL = document.getElementById("kernel").value;
            gl.shaderSource(fragshader, FSHADER_SOURCE + KERNEL);
            gl.compileShader(fragshader);
            var infof = gl.getShaderInfoLog(fragshader);
            gl.linkProgram(shaderProgram);
            gl.useProgram(shaderProgram);
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                var info = gl.getProgramInfoLog(shaderProgram);
                alert(infof + info);
            }
            glposition = gl.getAttribLocation(shaderProgram, 'position');
            glright = gl.getUniformLocation(shaderProgram, 'right');
            glforward = gl.getUniformLocation(shaderProgram, 'forward');
            glup = gl.getUniformLocation(shaderProgram, 'up');
            glorigin = gl.getUniformLocation(shaderProgram, 'origin');
            glx = gl.getUniformLocation(shaderProgram, 'x');
            gly = gl.getUniformLocation(shaderProgram, 'y');
            gllen = gl.getUniformLocation(shaderProgram, 'len');
        });
        document.getElementById("cancle").addEventListener("click", function() {
            document.getElementById("kernel").value = KERNEL;
        });
    }
    </script>
v

</body>
</html>
