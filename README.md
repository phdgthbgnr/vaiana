# vaiana
Code source Vaiana / Moana (gestion des déplacments dans une map (mobile/desktop))

````js
// 2. This code loads the IFrame Player API code asynchronously.
    var tag = document.createElement('script');
    tag.src = "https://www.youtube.com/player_api";
    //tag.src = "https://www.youtube.com/iframe_api";
    var firstScriptTag = document.getElementsByTagName('script')[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

    var player1, player2, curscreen = 0,
        urlfb = 'https://www.facebook.com/sharer/sharer.php?u=<?php echo urlencode('http://'.$host); ?>',
        lang = '<?php echo $lang ?>',
        rep = '<?php echo $rep ?>',
        urlspt = '<?php echo $urlspotify ?>',
        msgsoon =  '<p class="soon"><?php echo $trad[$lang]['soon'] ?></p>',
        mobile = '<?php echo $mobile ?>',
        moana = '<?php echo $moana ?>',
        albums = [
            {'album':1, 'poster':'aladdin.jpg', '<?php echo $albums[$lang]['aladdin'] ?>':{}},
            {'album':2, 'poster':'cinderella.jpg', '<?php echo $albums[$lang]['cinderella'] ?>':{}},
            {'album':3, 'poster':'frozen.jpg', '<?php echo $albums[$lang]['frozen'] ?>':{}},
            {'album':4, 'poster':'mulan.jpg', '<?php echo $albums[$lang]['mulan'] ?>':{}},
            {'album':5, 'poster':'peterpan.jpg', '<?php echo $albums[$lang]['peterpan'] ?>':{}},
            {'album':6, 'poster':'pinocchio.jpg', '<?php echo $albums[$lang]['pinocchio'] ?>':{}},
            {'album':7, 'poster':'pocahontas.jpg', '<?php echo $albums[$lang]['pocahontas'] ?>':{}},
            {'album':8, 'poster':'frog.jpg', '<?php echo $albums[$lang]['frog'] ?>':{}},
            {'album':9, 'poster':'tangled.jpg', '<?php echo $albums[$lang]['tangled'] ?>':{}},
            {'album':10,'poster':'junglebook.jpg', '<?php echo $albums[$lang]['junglebook'] ?>':{}},
            {'album':11,'poster':'lionking.jpg', '<?php echo $albums[$lang]['lionking'] ?>':{}},
            {'album':12,'poster':'bighero6.jpg', '<?php echo $albums[$lang]['bighero6'] ?>':{}},
            {'album':13,'poster':'mermaid.jpg', '<?php echo $albums[$lang]['mermaid'] ?>':{}},
            {'album':14,'poster':'moana.jpg', '<?php echo $albums[$lang]['moana'] ?>':{}},
            {'album':15,'poster':'baymax.jpg', '<?php echo $albums[$lang]['baymax'] ?>':{}}
        ],
        assets = [
            {'asset': ['<?php echo implode("','",$assets[$lang]['image']) ?>']},
            {'asset': ['<?php echo implode("','",$assets[$lang]['video']) ?>']},
            {'asset': ['<?php echo implode("','",$assets[$lang]['plst']) ?>']}
        ],

        voyages = <?php echo json_encode($voyages[$lang]) ?>;

    function onYouTubePlayerAPIReady() {
        // interstitiel video
        player1 = new YT.Player('player1', {
            width: '100%',
            height: '100%',
            videoId: '',
            playerVars: {'autoplay': 0, 'controls': 1, 'html5': 1 ,'enablejsapi':0,'showinfo':0,'iv_load_policy':3,'modestbranding':1,'rel':0},
            events: {
                'onReady': onPlayerReady1,
                'onStateChange': onPlayerStateChange1
            }
        });
        // player video BTS
        player2 = new YT.Player('player2', {
            width: '100%',
            height: '100%',
            videoId: '',
            playerVars: {'autoplay': 0, 'controls': 1, 'html5': 1 ,'enablejsapi':0,'showinfo':0,'iv_load_policy':3,'modestbranding':1,'rel':0},
            events: {
                'onReady': onPlayerReady2,
                'onStateChange': onPlayerStateChange2
            }
        });
    };

    // interstitiel video
    function onPlayerReady1(event) {
        //event.target.playVideo();
    };

    function onPlayerStateChange1(event) {
        switch(event.data){
            case -1:
                console.log('video non demarree');
                break;
            case 3:
                console.log('tampon video');
                break;
            case 1:
                console.log('lecture video');
                //startlevelTimer(300,5000,afterMinuterie);
                break;
             case 0:
                finintervideo();
                console.log('video arretee'); // fin video
                break;
            case 2:
                console.log('video en pause');
                break;
        }
    };


    // video BTS
    function onPlayerReady2(event) {
        //event.target.playVideo();
    };

    function onPlayerStateChange2(event) {
        switch(event.data){
            case -1:
                console.log('video non demarree');
                break;
            case 3:
                console.log('tampon video');
                break;
            case 1:
                console.log('lecture 2 video');
                //startlevelTimer(300,10000,afterMinuterie);
                break;
             case 0:
                finBTSvideo(true);
                console.log('video 2 arretee'); // fin video
                break;
            case 2:
                console.log('video 2 en pause');
                break;
        }
    };

    // closure
   (function(){
       /*
       window.navigator.sayswho = (function(){
            var ua = navigator.userAgent, tem,
            M = ua.match(/(opera|chrome|safari|firefox|msie|trident(?=\/))\/?\s*(\d+)/i) || [],
            P = navigator.platform;
            if(/trident/i.test(M[1])){
                tem =  /\brv[ :]+(\d+)/g.exec(ua) || [];
                return 'IE '+(tem[1] || '');
            }
            if(M[1] === 'Chrome'){
                tem = ua.match(/\b(OPR|Edge)\/(\d+)/);
                if(tem!= null) return tem.slice(1).join(' ').replace('OPR', 'Opera');
            }
            M = M[2]? [M[1], M[2]]: [navigator.appName, navigator.appVersion, '-?'];
            M[3] = P;
            if((tem = ua.match(/version\/(\d+)/i))!= null) M.splice(1, 1, tem[1]);
            return M.join(' ');
        })();

        alert(window.navigator.sayswho);
       */

        var urlspotify = window.urlspt, // utilise dans BTS pour lancer les playlists
        lang = window.lang,
        urlfb = window.urlfb,
        rep = window.rep,
        moana = window.moana,
        request = false,
        autoplay = true,        // joue toutes les pistes automatiquement
        curTracks = 0,          // piste en cours de lecture (numero de piste)
        totTracks = 0,          // total des pistes en cours de lecture (nombre total de pistes)
        curSongId = 0,          // id de la piste en cours
        audioTag,
        albums = window.albums,
        assets = window.assets,
        //token = window.token,
        voyages = window.voyages,
        msgsoon = window.msgsoon,
        mobile = parseInt(window.mobile),
        lang,
        tmap,               // twomaps
        map1,               // mapa
        map2,               // mapb
        hmap = 1.0,         // scale
        initmouseX = 0,     // mouseX on mousedown
        initmouseY = 0,     // mouseY on mousedown
        offsetx = 0,        // translate x
        offsety = 0,        // translate y
        oldoffsetx = 0,     // translate x on mouseup
        oldoffsety = 0,     // translate y on mouseup
        xmap = 4470,        // taille de la map
        xmap2 = 2235,       // taille de la double map divisee par 2 : 4470 / 2
        _htmap = 1079,      // hauteur de la map
        diffx = 0,          // ecart pinch horizontal
        diffy = 0,          // ecart pinch vertival
        zmax = 1.4,         // zoom max
        zmin = .7,          // zoom min
        _zoom = .02,        // increment zooming
        _timer,             // setInterval pour duree min video
        //poster = '',      // image de poster de la playlist album
        curbtsimg = 0,
        mapenable = true,
        imgload = 0,        // num image chargées
        interzoom = null,   // vrariable pour les boutons zoom;
        //firstClic = 0,    // premier clic sur la map
        started = 0,        // clic sur start = 1
        ochanged = 0,       // pour ne pas faire l'init de l'écran 50 fois
        currentnode = '',   // id du title cliqué

        $ID = function(id){
            var elem = null;
            if (document.getElementById(id) !== null) elem = document.getElementById(id);
            if(elem == null) console.log('%cERREUR : id "' + id + '" introuvable','color:#ff1d00;font-weight:bold');
            return elem;
        },

        addAclass = function (id, classe){
            $ID(id).classList ? $ID(id).classList.add(classe) : $ID(id).className += ' '+classe;
        },

        removeAclass = function(id,classe){
            $ID(id).className = $ID(id).className.replace(' ' + classe, '').replace(classe, '');
        },

        hasAclass = function(id, cls) {
            var element = $ID(id);
            return (' ' + element.className + ' ').indexOf(' ' + cls + ' ') > -1;
        },

        afterVoyage = function(){
            addAclass('interhtml','nodisplay');
            finintervideo();
        },

        getVoyages = function(){
            if(voyages.length == 0) return true;
            for(var i = 0; i < voyages.length; i++){
                if(voyages[i].type == 'yt' && voyages[i].dejavu == 0){
                    window.player1.loadVideoById(voyages[i].id);
                    voyages[i].dejavu = 1;
                    removeAclass('intervideo','nodisplay');
                    startlevelTimer(300,5000,afterMinuterie);
                    return false;
                    break;
                }
                if(voyages[i].type == 'html' && voyages[i].dejavu == 0){
                    voyages[i].dejavu = 1;
                    removeAclass('interhtml','nodisplay');
                    startlevelTimer(300,3500, afterVoyage);
                    return false;
                    break;
                }

            }
            return true;
        },

        getPoster = function (n){
            var rt = 'posterblnk.gif';
            for(var i = 0; i<albums.length; i++){
                //if(albums[i].hasOwnProperty(id)){
                if(albums[i].album == n){
                    rt = albums[i]['poster'];
                }
            }
            return rt;
        },

        getAlbum = function(id,h,n){

            var imgposter = getPoster(n);
            $ID('posters').src = '_img/' + rep + '/posters/'+imgposter;

            if(id !== ''){
                var src = 'https://embed.spotify.com/?uri=spotify:album:'+id+'&view=list';
                //var id = t.getAttribute('data-album');
                var res = '<iframe id="theframe" src="' + src + '" width="380" height="300" frameborder="0" allowtransparency="true"></iframe>';
                if(h == '#moana' && moana === '0' ) res += '<div class="pastille"><img src="_img/' + rep + '/pastille.png"/></div>';
                $ID('album').innerHTML = res;
            }else{
                $ID('album').innerHTML = '<div class="pastille"><img src="_img/' + rep + '/pastille.png"/></div>';
            }
            return false;

        },

        getScalefromZoom = function(){
            var st = window.getComputedStyle(tmap, null);
            var tr = st.getPropertyValue('transform');
            if(tr !== null){
                var values = tr.split('(')[1];
                values = values.split(')')[0];
                values = values.split(',');
                var _x = parseInt(values[4]);
                var _y = parseInt(values[5]);

                if(_x != 0) oldoffsetx = _x;
                if(_y != 0) oldoffsety = _y;
            }
        },

        stoplevelTimer = function(){
            clearInterval(_timer);
        },

        startlevelTimer = function(tick,duration,cb){
            var incr = (tick/duration)*100;
            var ll = $ID('minuterie');
            var pc = 0;
            var callback = cb;
            _timer = setInterval(function(){
                pc += incr;
                if(pc > 100) pc = 100;
                ll.style.width = pc+'%';
                if(pc == 100){
                    callback();
                    stoplevelTimer();
                }
           },tick);
        },

        finintervideo = function(){

            window.player1.stopVideo();
            var id = $ID('skip').getAttribute('data-album');
            var ref = $ID('skip').getAttribute('data-ref');
            var num = $ID('skip').getAttribute('data-num');
            autoplay = true;
            getAlbum(id,ref,num);
            addAclass('intervideo','nodisplay');
            addAclass('moana3','moanafanim');

        },

        finBTSvideo = function(f){
            addAclass('btsvideo','nodisplay');
            removeAclass('blocg','novisible');
            removeAclass('blocd','novisible');
            removeAclass('soon','novisible');
            if(f){
                removeAclass('close1','novisible');
                removeAclass('closevid','novisible');
                addAclass('skipbts','novisible');
                removeAclass('share1','novisible');

            }
        },

        refreshMap = function(){

            var tt = tmap.getBoundingClientRect();

            if(tt.width/2 < window.innerWidth){

                manageEvents.listenerRemove('contmap','mousemove', true);
                manageEvents.listenerRemove('contmap','touchmove',true);
                // centrage vertical
                oldoffsety = offsety = (window.innerHeight - _htmap)/2;
                // centrage horizontal
                oldoffsetx = offsetx =  (window.innerWidth - (xmap2*hmap))/2;

                tmap.style.transform = 'translate(' + offsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + offsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + offsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                addAclass('mapb','novisible');
                return false;
            }else{
                removeAclass('mapb','novisible');
            }

            if(tt.left>=0){
                oldoffsetx = offsetx = oldoffsetx - xmap2 * hmap;//(offsetx)-(xmap2);//*hmap);
                tmap.style.transform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                return true;
            }

            if(tt.right <= window.innerWidth){
                oldoffsetx = offsetx = oldoffsetx + xmap2 * hmap;
                oldoffsetx = offsetx-10;
                tmap.style.transform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                return true;
            }

            return false;

        },

        initMaps = function(nd, easing){

            var moa = $ID(nd).getBoundingClientRect();
            var imapa = map1.getBoundingClientRect();
            var mtop = $ID(nd).offsetTop;
            var mleft = $ID(nd).offsetLeft;
            var parnt = $ID(nd).parentNode.getAttribute('id');

            addAclass('twomaps','transmap');
            manageEvents.listenertransAdd('twomaps', 'transitionend', easing);

            var decal = parnt == 'mapb' ? xmap2 : 0;

            map1.style.left = '0px';
            map2.style.left = xmap2 + 'px';

            if(window.innerWidth > 1024) {
                hmap = .9;
            }

            if(window.innerWidth <= 1024 && window.innerWidth > 700) {
                zmin = .5;
                hmap = .7;

           }

            if(window.innerWidth <= 700) {
                zmin = .4;
                hmap = .6;
            }

            oldoffsetx = offsetx = -(mleft+decal)*hmap+(window.innerWidth/2)-((moa.width/2)*hmap);
            oldoffsety = offsety = ((_htmap*hmap)-_htmap)/2 - (mtop*hmap-window.innerHeight/2);

            tmap.style.transform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
            tmap.style.msTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
            tmap.style.WebkitTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';

            //refreshMap();
        },

        movemap = function(e,t,ct){

            if(!mapenable) return false;

            refreshMap();

            var x,y;
            var tt = tmap.getBoundingClientRect();

            if(tt.width < window.innerWidth) {
                manageEvents.listenerRemove('contmap','touchmove',true);
                manageEvents.listenerRemove('contmap','mousemove', true);
                return false;
            }
            var tlf = tt.left;
            var trg = tt.right;

            // zoom touch
            if(e.changedTouches && e.touches.length > 1){
                manageEvents.listenerRemove('contmap','touchmove',true);
                return false;
            }

            // > 2 touch : rien
            if(e.changedTouches && e.touches.length > 2){
                manageEvents.listenerRemove('contmap','touchmove',true);
                return false;
            }

            // touch
            if(e.changedTouches && e.touches.length == 1 && tlf  < 0){

                x = e.changedTouches[0].clientX;
                y = e.changedTouches[0].clientY;

                offsetx = x-initmouseX+oldoffsetx;
                offsety = y-initmouseY+oldoffsety;
                //tmap.style.transform = 'scale(' + hmap + ')';
                tmap.style.transform = 'translate(' + offsetx + 'px,' + offsety +'px) scale(' + hmap + ')';

            }


            // pas touch
            if(!e.changedTouches && (tlf <= 0 || trg > window.innerWidth)){
                x = e.x === undefined ? e.clientX : e.x;
                y = e.y === undefined ? e.clientY : e.y;

                offsetx = x-initmouseX+oldoffsetx;
                offsety = y-initmouseY+oldoffsety;

                tmap.style.transform = 'translate(' + offsetx + 'px,' + offsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + offsetx + 'px,' + offsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + offsetx + 'px,' + offsety +'px) scale(' + hmap + ')';
                //refreshMap();
            }


            return false;
        },

        handlezoom = function(e,t,ct){
            if(e.changedTouches && e.touches.length > 1){
                var xdiff1 = e.touches[0].clientX;
                var xdiff2 = e.touches[1].clientX;
                var ydiff1 = e.touches[0].clientY;
                var ydiff2 = e.touches[1].clientY;

                var xdiff = Math.abs(xdiff1 - xdiff2);
                var ydiff = Math.abs(ydiff1 - ydiff2);
                //zoom
                if (xdiff > diffx &&  ydiff > diffy){
                    hmap+= _zoom;
                }

                if (xdiff < diffx &&  ydiff < diffy){
                    hmap-= _zoom;
                }


                hmap = hmap > zmax ? zmax : hmap;
                hmap = hmap < zmin ? zmin : hmap;

                tmap.style.transform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';

                getScalefromZoom();
                refreshMap();

                diffx = xdiff;
                diffy = ydiff;

            }

            return false;
        },

        handletouchstart = function(e,t,ct){
            mapenable = true;
            if(!hasAclass('instruct','nodisplay')) addAclass('instruct','nodisplay');
            // zoom touch
            if(e.changedTouches && e.touches.length == 2){
                var xdiff1 = e.touches[0].clientX;
                var xdiff2 = e.touches[1].clientX;
                var ydiff1 = e.touches[0].clientY;
                var ydiff2 = e.touches[1].clientY;
                diffx = Math.abs(xdiff1 - xdiff2);
                diffy = Math.abs(ydiff1 - ydiff2);
                manageEvents.listenerRemove('contmap','touchmove',true);
                manageEvents.listenerAdd('contmap','touchmove', handlezoom, true);
                return false;
            }

            // > 2 touch : rien
            if(e.changedTouches && e.touches.length > 2){
                return false;
            }

            var x,y;

            if(e.changedTouches && e.touches.length == 1){
                x = e.changedTouches[0].clientX;
                y = e.changedTouches[0].clientY;
                //manageEvents.listenerAdd('contmap','touchmove', movemap, true);
                manageEvents.listenerAdd('contmap','touchmove', movemap, true);

                initmouseX = x;
                initmouseY = y;
                tmap.style.transform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
            }

            return false;
        },

        mousehdown = function(e,t,ct){

            //firstClic = 1;
            var x,y;
            if(!hasAclass('instruct','nodisplay')) addAclass('instruct','nodisplay');
            // pas touch
            if(!e.changedTouches){
                x = e.x === undefined ? e.clientX : e.x;
                y = e.y === undefined ? e.clientY : e.y;

            }

            var tt = tmap.getBoundingClientRect();
            if(tt.width/2 < window.innerWidth && tt.height < window.innerHeight ) {
                initmouseX = x;
                initmouseY = y;
                mapenable = true;
                return false;
            }

            mapenable = true;
            initmouseX = x;
            initmouseY = y;
            tmap.style.transform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
            tmap.style.msTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
            tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
            manageEvents.listenerAdd('contmap','mousemove', movemap, true);
            return false;
        },

        handleup = function(e,t,ct){
            mapenable = false;
            manageEvents.listenerRemove('contmap','mousemove', true);
            manageEvents.listenerRemove('contmap','touchmove', true);
            //addAclass('blocking','nodisplay');
            // > 2 touch : rien
            if(e.changedTouches && e.touches.length > 1){
                return false;
            }

            diffx = diffy = 0;

            oldoffsetx = offsetx;
            oldoffsety = offsety;

            // pour firefox
            //if(!e.changedTouches){
                //var x,y;
                //x = e.x === undefined ? e.clientX : e.x;
                //y = e.y === undefined ? e.clientY : e.y;
                /*
                if(Math.abs(x - initmouseX) < 10 || Math.abs(y - initmouseY) < 10){
                    manageEvents.listenerRemove('contmap','mousemove', true);
                    //manageEvents.listenerRemove('contmap','mousemove', true);
                    //manageEvents.listenerRemove('contmap','touchmove');
                }
                */

            //}


            centerVertical();

            return false;

        },

        centerVertical = function(){

            var tt = tmap.getBoundingClientRect();

            // recalage de la map au milieu
            if(tt.height < window.innerHeight){
                oldoffsety = offsety = (window.innerHeight - _htmap)/2;
                addAclass('twomaps','transmapb');
                manageEvents.listenertransAdd('twomaps', 'transitionend', transend2);
                tmap.style.transform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                return false;
            }

            // recalage de la map en haut
            if(tt.top > 0){
                oldoffsety = offsety = ((_htmap*hmap)-_htmap)/2;
                addAclass('twomaps','transmapb');
                manageEvents.listenertransAdd('twomaps', 'transitionend', transend2);
                tmap.style.transform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                //refreshMap();
            }

            // recalage de la map en bas
            if(tt.bottom < window.innerHeight){
                oldoffsety = offsety = window.innerHeight - ((_htmap*hmap)+_htmap)/2;
                addAclass('twomaps','transmapb');
                manageEvents.listenertransAdd('twomaps', 'transitionend', transend2);
                tmap.style.transform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px, ' + offsety +'px) scale(' + hmap + ')';

            }

        },

        manualZoomd = function(e, t, ct){

            if(!hasAclass('instruct','nodisplay')) addAclass('instruct','nodisplay');

            var id = t.getAttribute('id');

            interzoom = setInterval(function(){
                hmap = id == 'zplus' ? hmap + _zoom : hmap - _zoom;
                hmap = hmap > zmax ? zmax : hmap;
                hmap = hmap < zmin ? zmin : hmap;
                tmap.style.transform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
                getScalefromZoom();
                //refreshMap();
            },100);

            return false;
        },

        manualZoomu = function(e, t, ct){

            refreshMap();
            clearInterval(interzoom);

            return false;
        },

        wheelMouse = function(e, t, ct){
            if(!hasAclass('instruct','nodisplay')) addAclass('instruct','nodisplay');
            var delta = Math.max(-1, Math.min(1, e.deltaY));
            if(delta == -1){
                hmap+=.1;
            }
            if(delta == 1){
                hmap-=.1;
            }
            hmap = hmap > zmax ? zmax : hmap;
            hmap = hmap < zmin ? zmin : hmap;
            tmap.style.transform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
            tmap.style.msTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';
            tmap.style.WebkitTransform = 'translate(' + oldoffsetx + 'px,' + oldoffsety +'px) scale(' + hmap + ')';

            getScalefromZoom();

            refreshMap();
        },

        reSize = function (e,t,ct){
            initSlider();
            /*
            if(firstClic == 0) {
                //centerMap();
            }else{

            }*/
            if(started == 1) refreshMap();
        },

        afterMinuterie = function(){
            addAclass('minutes','fadeout');
            addAclass('skip','fadein');
        },

        transend = function(e,t,ct){
            removeAclass('twomaps','transmap');
            removeAclass('instruct','instzero');
            addAclass('mapa','vawes');
            addAclass('mapb','vawes');
            manageEvents.listenerRemove('twomaps', 'transitionend');
        },

        transend2 = function(e,t,ct){
            removeAclass('twomaps','transmapb');
            manageEvents.listenerRemove('twomaps', 'transitionend');
        },

        transend3 = function(e,t,ct){
            removeAclass('twomaps','transmap');
            refreshMap();
            centerVertical();
            addAclass('mapa','vawes');
            addAclass('mapb','vawes');
            manageEvents.listenerRemove('twomaps', 'transitionend');
        },

        clicthmb = function(e,t,ct){
            var lien = t.getAttribute('href');
            var typ = t.getAttribute('data-type');
            switch(typ){
                case 'images':
                    $ID('affiche').src = '_img/' + lang + '/bts/big/' + lien + '.jpg';
                break;
                case 'youtube':
                    removeAclass('btsvideo','nodisplay');
                    window.player2.loadVideoById(lien);
                    addAclass('blocg','novisible');
                    addAclass('blocd','novisible');
                    addAclass('soon','novisible');
                break;
                case 'playlist':
                  // redirection direct sur click
                break;
            }
            return false;
        },

        displayBTS = function(i,ap){ // ap = autoplay (premier lancement)
            var ret = '<ul id="sliding">';
            var rep = i == 0 ? 'lite/' : i == 1 ? 'thumbvid/' : 'thumbplst/';
            var clas = i == 0 ? 'trois' : i == 1 ? 'deux' : 'trois';
            var typ = i == 0 ? 'images' : i == 1 ? 'youtube' : 'playlist';
            var tag = '';

            removeAclass('thimg','sel');
            removeAclass('thvideo','sel');
            removeAclass('thplst','sel');

            var arrow = '';

            if(i == 0) { addAclass('thimg','sel'); tag = 'ga-thimg-';}    // images
            if(i == 1) { addAclass('thvideo','sel'); tag = 'ga-thvideo-'; arrow = '<img src="_img/arrow-video.png" class="arrow"/>'; }  // videos
            if(i == 2) { addAclass('thplst','sel'); tag = 'ga-thplst-';}   // playlist

            for (var t=0; t<assets[i].asset.length; t++){
                var asst = assets[i].asset[t];
                var u = '';
                if(i == 2) u = urlspotify;
                ret += '<li class="' + clas + '"><a href="' + u + asst + '" id="thmb' + t + '" data-type="' + typ + '" target="_blank">' + arrow + '<img src="_img/' + lang + '/bts/' + rep + asst + '.jpg" id="' + tag + (t+1) + '" class="fond"/></a></li>';
            }

            ret +='</ul>';
            $ID('thumb').innerHTML = '<div class="liste" id="liste">' + ret + '<div id="gutter" class="gutter novisible"><div class="slider" id="slider"></div></div></div>';

            for (var t=0; t<assets[i].asset.length; t++){
                if(i == 2){
                    manageEvents.listenerAdd('thmb'+t,'click', clicthmb, false);
                }else{
                    manageEvents.listenerAdd('thmb'+t,'click', clicthmb, true);
                }
            }

            if(ap){
                removeAclass('btsvideo','nodisplay');
                window.player2.loadVideoById(assets[i].asset[0]);
                addAclass('blocg','novisible');
                addAclass('blocd','novisible');
                addAclass('close1','novisible');
                addAclass('closevid','novisible');
                removeAclass('skipbts','novisible');
                addAclass('share1','novisible');
                addAclass('soon','novisible');
            }

            var mm = setTimeout(function(){
                  initSlider();
              },800);
        },

        clicnothing = function(e, t, ct){
            return false;
        },

        clic = function(e, t, ct){

            var href = '';
            try{
                href = t.getAttribute('href');
            }catch(err){
                href = '';
            }

            if(href !== ''){
                switch(href){
                    case '#start':
                        started = 1;
                        for (var i = 1; i < 16; i++){
                            var id1 = 'film'+i+'a';
                            var id2 = 'film'+i+'b';
                            removeAclass(id1,'opaczero');
                            removeAclass(id2,'opaczero');
                        }
                        removeAclass('boat1','opaczero');
                        removeAclass('boat2','opaczero');
                        //addAclass('moanabig','transmo');
                        removeAclass('footermap1','novisible');
                        removeAclass('behind','novisible');
                        removeAclass('socials','novisible');
                        removeAclass('zooms','novisible');
                        addAclass('page1','novisible');
                        removeAclass('mapa','novisible');
                        removeAclass('mapb','novisible');
                        addAclass('loader','nodisplay');

                        initMaps('film11a', transend);
                    break;
                    case '#behind':
                        addAclass('behind','novisible');
                        addAclass('footermap1','novisible');
                        if(assets[1].asset[0] != ''){
                            displayBTS(1,true); // videos
                        }else{
                            if(assets[2].asset[0] != ''){
                                displayBTS(2,false); // playlists
                                addAclass('skipbts','novisible');
                            }
                        }

                        removeAclass('page7','novisible');
                        addAclass('moana2','moanafanim');
                        removeAclass('mapa','vawes');
                        removeAclass('mapb','vawes');
                        addAclass('zooms','novisible');
                        removeAclass('mapa','vawes');
                        removeAclass('mapb','vawes');
                    break;
                    case '#images':
                        displayBTS(0,false);
                    break;
                    case '#videos':
                        displayBTS(1,false);
                    break;
                    case '#playlists':
                        displayBTS(2,false);
                    break;
                    case '#closevideo':
                        //window.player2.seekTo(0);
                        window.player2.stopVideo();
                        removeAclass('blocg','novisible');
                        removeAclass('blocd','novisible');
                        addAclass('btsvideo','nodisplay');
                        removeAclass('soon','novisible');
                    break;
                    case '#suiv':
                        if(curbtsimg < assets[0].asset.length-1){
                            curbtsimg++;
                            $ID('affiche').src = '_img/' + lang + '/bts/big/' + assets[0].asset[curbtsimg] + '.jpg';
                        }
                    break;
                    case '#prec':
                        if(curbtsimg > 0){
                            curbtsimg--;
                            $ID('affiche').src = '_img/' + lang + '/bts/big/' + assets[0].asset[curbtsimg] + '.jpg';
                        }
                    break;
                    case '#continue1': // BTS
                        //var id = $ID('page7').getAttribute('id');
                        $ID('thumb').innerHTML = '';
                        addAclass('page7','novisible');
                        removeAclass('footermap1','novisible');
                        removeAclass('behind','novisible');
                        removeAclass('blocg','novisible');
                        removeAclass('blocd','novisible');
                        //window.player2.seekTo(0);
                        window.player2.pauseVideo();
                        addAclass('btsvideo','nodisplay');
                        removeAclass('moana2','moanafanim');
                        addAclass('mapa','vawes');
                        addAclass('mapb','vawes');
                        removeAclass('zooms','novisible');
                        addAclass('mapa','vawes');
                        addAclass('mapb','vawes');
                    break;
                    case '#continue2': // playlist album
                        $ID('album').innerHTML = '';
                        addAclass('albums','novisible');
                        $ID('posters').src = '_img/loading.gif';
                        removeAclass('footermap1','novisible');
                        removeAclass('behind','novisible');
                        removeAclass('moana3','moanafanim');
                        removeAclass('zooms','novisible');
                        initMaps(currentnode, transend3);

                        //audioTag.pause();
                    break;
                    case '#skip':
                        finintervideo();
                    break;
                    case '#skipbts':
                        finBTSvideo(true);
                        //window.player2.seekTo(0);
                        window.player2.pauseVideo();
                    break;
                        /*
                    case '#plus':
                        manualZoom(1);
                    break;
                    case '#moins':
                        manualZoom(-1);
                    break;
                    */
                }
            }
            return false;
        },

        clicfilms = function(e,t,ct){

            mapenable = false;

            manageEvents.listenerRemove('contmap','mousemove', true);
            manageEvents.listenerRemove('contmap','touchmove',true);

            var x,y;

            // si pas clic on zap
            if(!e.changedTouches){
                x = e.x === undefined ? e.clientX : e.x;
                y = e.y === undefined ? e.clientY : e.y;
                if(x != initmouseX || y != initmouseY){
                    return false;
                }
            }

            // si resultat d'un swipe on zap
            if(e.changedTouches){
                x = e.changedTouches[0].clientX;
                y = e.changedTouches[0].clientY;
                if(Math.abs(x - initmouseX) > 10 || Math.abs(y - initmouseY) > 10){
                    diffx = diffy = 0;
                    oldoffsetx = offsetx;
                    oldoffsety = offsety;
                    return false;
                }
            }

            var href = '';
            try{
                href = t.getAttribute('href');
            }catch(err){
                href = '';
            }

            if(href !== ''){
                switch(href){
                    case '#peterpan':
                    case '#cinderella':
                    case '#frozen':
                    case '#tangled':
                    case '#mermaid':
                    case '#pinocchio':
                    case '#aladdin':
                    case '#mulan':
                    case '#lionking':
                    case '#junglebook':
                    case '#moana':
                    case '#pocahontas':
                    case '#bighero6':
                    case '#baymax':
                    case '#frog':

                        /*
                        // en hover une fois clique
                        var num = t.getAttribute('data-num');
                        var cls = 'dejavu'+num;
                        addAclass('film'+num+'a',cls);
                        addAclass('film'+num+'b',cls);
                        */
                        removeAclass('mapa','vawes');
                        removeAclass('mapb','vawes');
                        autoplay = getVoyages();
                        curTracks = 0;
                        curSongId = 0;
                        var num = t.getAttribute('data-num');
                        var cur = t.getAttribute('id');
                        var id = t.getAttribute('data-album');
                        $ID('skip').setAttribute('data-album',id);
                        $ID('skip').setAttribute('data-ref',href);
                        $ID('skip').setAttribute('data-num',num);
                        addAclass('footermap1','novisible');
                        addAclass('behind','novisible');
                        addAclass('zooms','novisible');

                        removeAclass('albums','novisible');
                        var bx = t.getAttribute('data-bx');
                        var by = t.getAttribute('data-by');
                        $ID('boat1').style.top = by+'%';
                        $ID('boat1').style.left = bx+'%';
                        $ID('boat2').style.top = by+'%';
                        $ID('boat2').style.left = bx+'%';
                        if(autoplay) addAclass('moana3','moanafanim');
                        getAlbum(id,href,num);
                        currentnode = cur;

                    break;
                }
            }

            return false;
        },

        imgloaded = function(e, t, ct, im, tot){
            imgload++;
            //$ID('loading').style.width = Math.round((imgload/tot)*100)+'%';
            $ID('loading').style.width = Math.round((imgload/tot)*100)+'%';
            //console.log(imgload);
            //console.log(tot);
            if(imgload >= tot) {
              var mm = setTimeout(function(){
                  addAclass('loader','nodisplay');
                  clearTimeout(mm);
              },1000);

            }

        },

        // slider BTS ----------------------------------------------------------------------------------------------------

        slider = {
            'touchstarty':0,    // coord touchstart Y
            'boundlist':{},     // getBoundingClientRect() de liste
            'contH':0,          // hauteur du conteneur de la liste
            'lsty':0,           // coord Y de la liste
            'lstH':0,           // hauteur de la liste
            'gutY':0,           // coord Y du gutter    --> ascenceur
            'gutH':0,           // hauteur du gutter    --> ascenceur
            'slY':0,            // coord Y slider       --> ascenceur
            'slH':0,            // hauteur du slider    --> ascenceur
            'ulY':0,            // coord Y ul à slider
            'ulH':0,            // hauteur ul à slider
            'offY':0,           // unite decalage Y ul à slider
            'lineH':0,          // hauteur li (== offY)
            'offYd':0 ,         // division hauteur ul à slider
            'deltalist':0       // difference entre ulH - contH
        },

        initSlider = function(){
            if($ID('sliding') !== null){
                var n = $ID('sliding').childNodes.length;
                slider.contH        = $ID('liste').offsetHeight;
                slider.lstH         = $ID('liste').childNodes[0].offsetHeight;
                slider.gutH         = $ID('gutter').offsetHeight;
                slider.ulY          = $ID('sliding').offsetTop;
                slider.ulH          = $ID('sliding').offsetHeight;
                slider.lineH        = $ID('sliding').childNodes[0].offsetHeight;
                slider.offY         = slider.ulH/n;
                slider.boundlist    = $ID('liste').getBoundingClientRect();
                slider.offYd        = slider.offY/2;
                slider.deltalist    = slider.ulH-slider.contH;
                slider.slH          = slider.deltalist;

                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';

                $ID('sliding').style.WebkitTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.WebkitTransform = 'translate(0px,' + slider.ulY + 'px)';

                $ID('slider').style.height = (slider.gutH-slider.slH)+'px';
                if(slider.deltalist <= 0) {
                    addAclass('gutter','novisible');
                }else{
                    removeAclass('gutter','novisible');
                }

                if(slider.contH < slider.lstH){
                    manageEvents.listenerAdd('liste','touchstart',slidetouchstart,false);
                    manageEvents.listenerAdd('liste','mousewheel',slidewheelMouse,true);
                    manageEvents.listenerAdd('liste','DOMMouseScroll',slidewheelMouse,true);
                    manageEvents.listenerAdd('liste','wheel',slidewheelMouse,true);
                    removeAclass('gutter','novisible');
                }else{
                    manageEvents.listenerRemove('liste','touchstart');
                    manageEvents.listenerRemove('liste','mousewheel');
                    manageEvents.listenerRemove('liste','DOMMouseScroll');
                    manageEvents.listenerRemove('liste','wheel');
                    if(!hasAclass('gutter','novisible')) addAclass('gutter','novisible');
                }
            }

        },

        slidetouchstart = function(e, t, ct){
            slider.touchstarty = e.touches[0].clientY;
            manageEvents.listenerAdd('liste','touchmove',slidetouchmove,true);
            manageEvents.listenerAdd('liste','touchend',slidetouchend,false);
            manageEvents.listenerAdd('liste','touchleave',slidetouchend,false);
            return false;
        },

        slidetouchmove = function(e, t, ct){
            var ytop = $ID('sliding').getBoundingClientRect().top;
            var ybott = $ID('sliding').getBoundingClientRect().bottom;

            /*
            if(ybott > slider.boundlist.bottom){
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)'
            }
            */

            if(e.touches[0].clientY < slider.touchstarty && ybott > slider.boundlist.bottom) {
                slider.ulY -= slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
            }

            if(e.touches[0].clientY > slider.touchstarty && ytop < slider.boundlist.top) {
                slider.ulY += slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
            }

            if(ytop > slider.boundlist.top){
                slider.ulY = 0;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
            }

            slider.touchstarty = e.touches[0].clientY;

            return false;
        },

        slidetouchend = function(e, t, ct){
            manageEvents.listenerRemove('liste','touchmove');
            manageEvents.listenerRemove('liste','touchend');
            manageEvents.listenerRemove('liste','touchleave');
        },

        slidewheelMouse = function(e, t, ct){
                //var delta = Math.max(-1, Math.min(1, (e.wheelDelta || -e.detail)));
            var delta = Math.max(-1, Math.min(1, e.deltaY));
            var ytop = $ID('sliding').getBoundingClientRect().top;
            var ybott = $ID('sliding').getBoundingClientRect().bottom;

            if(delta == -1 && ytop > slider.boundlist.top){
                slider.ulY = 0;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';

                $ID('sliding').style.WebkitTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.WebkitTransform = 'translate(0px,' + -slider.ulY + 'px)';
                return false;
            }

            if(delta == 1 && ybott <= slider.boundlist.bottom-slider.offYd){
                slider.ulY = -slider.deltalist;//slider.contH - slider.lstH;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';

                $ID('sliding').style.WebkitTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.WebkitTransform = 'translate(0px,' + -slider.ulY + 'px)';
                return false;
            }

            if(delta == 1 && ybott >= slider.boundlist.bottom) {
                slider.ulY -= slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';

                $ID('sliding').style.WebkitTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.WebkitTransform = 'translate(0px,' + -slider.ulY + 'px)';
            }

            if(delta == -1 && ytop < slider.boundlist.top) {
                slider.ulY += slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';

                $ID('sliding').style.WebkitTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.WebkitTransform = 'translate(0px,' + -slider.ulY + 'px)';
            }

            return false;
        },

        shareFB = function(e,t,ct){
            var w = 600, h = 400,
            t = window.innerHeight/2-h/2, l = window.innerWidth/2-w/2;
            window.open(urlfb, 'facebook', 'width=600, height=400, top=' + t + ', left=' + l +  ' scrollbars=no');
            return false;
        }

        // FIN slider BTS -----------------------------------------------------------------------------------------------

        // ORIENTATION

        manageEvents.listenerAdd(window,'orientationchange', function(e,t,ct){

            if(started == 0 && window.innerWidth > window.innerHeight && ochanged == 0 ){
                tmap = $ID('twomaps');
                map1 = $ID('mapa');
                map2 = $ID('mapb');
                map1.style.top = '0px';
                map2.style.top = '0px';
                map1.style.left = '0px';
                map2.style.left = xmap2 + 'px';
                var moa = $ID('film11a').getBoundingClientRect();
                var imapa = map1.getBoundingClientRect();

                oldoffsetx = offsetx = ((window.innerWidth/2 - moa.left)*hmap)-moa.width/2;
                oldoffsety = offsety = -(moa.top+imapa.top)*hmap+window.innerHeight/2;
                hmap = 1.2;
                tmap.style.transform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                ochanged = 1;
            };

            window.player1.pauseVideo();
            window.player2.pauseVideo();

            var orientation = t.orientation;
           if (orientation !== undefined){
                var ort = setTimeout(function(){
                    var wnw = window.innerWidth;
                    var scrw = screen.width;
                    var scrh = screen.height;
                    var wnh = window.innerHeight;
                    if(wnw > wnh){
                        if(scrh > scrw) scrh = scrw;
                        var offset = scrh - wnh;
                        document.body.style.height = scrh+'px';
                        setTimeout( function(){
                            window.scrollTo(0,0);
                        }, 10 );

                    };
                },300);
            };


        })

        // EVENTS

        manageEvents.listenerAdd(document,'DOMContentLoaded', function(e,t,ct){


            manageEvents.ImageLoader.listenerAdd('img','load', imgloaded, true);

            tmap = $ID('twomaps');
            map1 = $ID('mapa');
            map2 = $ID('mapb');

            manageEvents.listenerAdd('contmap','touchstart', handletouchstart, true);
            manageEvents.listenerAdd('contmap','mousedown', mousehdown, true);
            manageEvents.listenerAdd('contmap','touchend', handleup, false);
            manageEvents.listenerAdd('contmap','mouseup', handleup, true);
           // manageEvents.listenerAdd('contmap','click', clicmap, true);
            manageEvents.listenerAdd('contmap','mousewheel',wheelMouse, false);
            manageEvents.listenerAdd('contmap','DOMMouseScroll',wheelMouse, false);
            manageEvents.listenerAdd('contmap','wheel', wheelMouse, false);

            manageEvents.listenerAdd(window,'resize', reSize, true);

            manageEvents.listenerAdd('start','click', clic, true);
            manageEvents.listenerAdd('behind','click', clic, true);

            manageEvents.listenerAdd('thimg','click', clic, true);
            manageEvents.listenerAdd('thvideo','click', clic, true);
            manageEvents.listenerAdd('thplst','click', clic, true);

            manageEvents.listenerAdd('prec','click', clic, true);
            manageEvents.listenerAdd('suiv','click', clic, true);

            //manageEvents.listenerAdd('continue1','click', clic, true);
            manageEvents.listenerAdd('close1','click', clic, true);
            manageEvents.listenerAdd('continue2','click', clic, true);
            manageEvents.listenerAdd('close2','click', clic, true);

            manageEvents.listenerAdd('closevid','click', clic, true);

            manageEvents.listenerAdd('skip','click', clic, true);
            manageEvents.listenerAdd('skipbts','click', clic, true);


            manageEvents.listenerAdd('zplus','click', clicnothing, true);
            manageEvents.listenerAdd('zmin','click', clicnothing, true);

            manageEvents.listenerAdd('zplus','mousedown', manualZoomd, true);
            manageEvents.listenerAdd('zmin','mousedown', manualZoomd, true);

            manageEvents.listenerAdd('zplus','touchstart', manualZoomd, true);
            manageEvents.listenerAdd('zmin','touchstart', manualZoomd, true);

            manageEvents.listenerAdd('zplus','mouseup', manualZoomu, true);
            manageEvents.listenerAdd('zmin','mouseup', manualZoomu, true);

            manageEvents.listenerAdd('zplus','touchend', manualZoomu, true);
            manageEvents.listenerAdd('zmin','touchend', manualZoomu, true);

            manageEvents.listenerAdd('share1','click', shareFB, true);
            manageEvents.listenerAdd('share2','click', shareFB, true);
            manageEvents.listenerAdd('share3','click', shareFB, true);



            //centrage de la map avec un zoom
            if (window.innerWidth > window.innerHeight){
                var moa = $ID('film11a').getBoundingClientRect();
                var imapa = map1.getBoundingClientRect();
                map1.style.left = '0px';
                map2.style.left = xmap2 + 'px';
                oldoffsetx = offsetx = ((window.innerWidth/2 - moa.left)*hmap)-moa.width/2;
                oldoffsety = offsety = -(moa.top+imapa.top)*hmap+window.innerHeight/2;
                hmap = 1.2;
                tmap.style.transform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                tmap.style.msTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                tmap.style.WebkitTransform = 'translate(' + offsetx + 'px,' + offsety + 'px) scale(' + hmap + ')';
                ochanged = 1;
            }


            for (var i = 1; i < 16; i++){
                var id1 = 'film'+i+'a';
                var id2 = 'film'+i+'b';

                manageEvents.listenerAdd(id1, 'click', clicfilms, true);
                manageEvents.listenerAdd(id2, 'click', clicfilms, true);

                manageEvents.listenerAdd(id1, 'touchend', clicfilms, true);
                manageEvents.listenerAdd(id2, 'touchend', clicfilms, true);
            }

        },true);

        // function exposed
        window.startlevelTimer = startlevelTimer;
        window.afterMinuterie = afterMinuterie;
        window.finintervideo = finintervideo;
        window.finBTSvideo = finBTSvideo;

       // effacement données globales
       window.albums = null;
       window.assets = null;
       //window.token = null;
       window.lang = null;
       window.urlspt = null;
       window.voyages = null;
       window.msgsoon = null;
       window.urlfb = null;
       window.rep = null;
       window.moana = null;
    // end closure
    })();
```


Code Source Dev player spécifique (not in production)
```js
var playlistURLs = [],
        urlspotify = window.urlspt, // utilise dans BTS pour lancer les playlists 
        lang = window.lang,
        request = false,
        autoplay = true,        // joue toutes les pistes automatiquement
        curTracks = 0,          // piste en cours de lecture (numero de piste)
        totTracks = 0,          // total des pistes en cours de lecture (nombre total de pistes)
        curSongId = 0,          // id de la piste en cours
        audioTag,
        albums = window.albums,     
        assets = window.assets,
        token = window.token,
        lang,
        tmap,               // twomaps
        map1,               // mapa   
        map2,               // mapb
        hmap = 1.0,         // scale
        initmouseX = 0,     // mouseX on mousedown
        initmouseY = 0,     // mouseY on mousedown
        offsetx = 0,        // translate x
        offsety = 0,        // translate y
        oldoffsetx = 0,     // translate x on mouseup
        oldoffsety = 0,     // translate y on mouseup
        xmap2 = 2235,       // taille de la double map divisee par 2 : 4470 / 2
        diffx = 0,          // ecart pinch horizontal
        diffy = 0,          // ecart pinch vertival
        zmax = 1.4,         // zoom max
        zmin = .8,          // zoom min
        _timer,             // setInterval pour duree min video
        //poster = '',        // image de poster de la playlist album
        curbtsimg = 0,
        mapenable = true,
            
        $ID = function(id){
            var elem = null;
            if (document.getElementById(id) !== null) elem = document.getElementById(id);
            if(elem == null) console.log('%cERREUR : id "' + id + '" introuvable','color:#ff1d00;font-weight:bold');
            return elem;
        },
            
        addAclass = function (id, classe){
            $ID(id).classList ? $ID(id).classList.add(classe) : $ID(id).className += ' '+classe;
        },

        removeAclass = function(id,classe){
            $ID(id).className = $ID(id).className.replace(' ' + classe, '').replace(classe, '');
        },

        hasAclass = function(id, cls) {
            var element = $ID(id);
            return (' ' + element.className + ' ').indexOf(' ' + cls + ' ') > -1;
        },
           
        
        // gestion album et slider ----------------------------------------------------------------------------------------------------
           
        slider = {
            'touchstarty':0,    // coord touchstart Y
            'boundlist':{},     // getBoundingClientRect() de liste
            'contH':0,          // hauteur du conteneur de la liste
            'lsty':0,           // coord Y de la liste
            'lstH':0,           // hauteur de la liste
            'gutY':0,           // coord Y du gutter    --> ascenceur
            'gutH':0,           // hauteur du gutter    --> ascenceur
            'slY':0,            // coord Y slider       --> ascenceur
            'slH':0,            // hauteur du slider    --> ascenceur
            'ulY':0,            // coord Y ul à slider
            'ulH':0,            // hauteur ul à slider
            'offY':0,           // unite decalage Y ul à slider
            'lineH':0,          // hauteur li (== offY)
            'offYd':0 ,         // division hauteur ul à slider
            'deltalist':0       // difference entre ulH - contH
        },
            
        initSlider = function(){
            if($ID('sliding') !== null){
                var n = $ID('sliding').childNodes.length;
                slider.contH        = $ID('liste').offsetHeight;
                slider.lstH         = $ID('liste').childNodes[0].offsetHeight;
                slider.gutH         = $ID('gutter').offsetHeight;
                slider.ulY          = $ID('sliding').offsetTop;
                slider.ulH          = $ID('sliding').offsetHeight;
                slider.lineH        = $ID('sliding').childNodes[0].offsetHeight;
                slider.offY         = slider.ulH/n;
                slider.boundlist    = $ID('liste').getBoundingClientRect(); 
                slider.offYd        = slider.offY/2;
                slider.deltalist    = slider.ulH-slider.contH;
                slider.slH          = slider.deltalist;
                
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                
                //console.log(slider);
                $ID('slider').style.height = (slider.gutH-slider.slH)+'px';
                if(slider.deltalist <= 0) {
                    addAclass('gutter','novisible');
                }else{
                    removeAclass('gutter','novisible');
                }
                
                if(slider.contH < slider.lstH){
                    manageEvents.listenerAdd('liste','touchstart',slidetouchstart,false);
                    manageEvents.listenerAdd('liste','mousewheel',slidewheelMouse,true);
                    manageEvents.listenerAdd('liste','DOMMouseScroll',slidewheelMouse,true);
                    manageEvents.listenerAdd('liste','wheel',slidewheelMouse,true);
                    removeAclass('gutter','novisible');
                }else{
                    manageEvents.listenerRemove('liste','touchstart');
                    manageEvents.listenerRemove('liste','mousewheel');
                    manageEvents.listenerRemove('liste','DOMMouseScroll');
                    manageEvents.listenerRemove('liste','wheel');
                    if(!hasAclass('gutter','novisible')) addAclass('gutter','novisible');
                }
            }
            
        },
           
        getRefOnSPTF = function(url, datas, callback, id, autoplay){
             manageEvents.promises.httpRequest(url,'POST', datas, 10000).then(function(e){
                try{
                    var myjson = JSON.parse(e);
                }catch(e){
                    var myjson = '';
                }
                 
                 if(typeof myjson === 'object'){
                     for(var i = 0; i < albums.length; i++){
                        if(albums[i].hasOwnProperty(id)) {
                           if(Object.keys(albums[i][id]).length === 0) albums[i][id] = myjson;
                        }
                     }
                     callback(myjson,id);
                     if(autoplay){   
                         if(myjson.tracks){
                            var track = myjson.tracks.items[0];
                            audioTag.src = track.preview_url;
                            audioTag.play();
                        }
                    }
                 }
                 
                 manageEvents.promises.request = false;
                 request = false;
                 
            }).fail(function(error){
              console.log(error);
              console('erreur');
              request = false;
            }).progress(function(progress){
                console.log(Math.round(progress*100) + ' %');
            }).fin(function(){  // finally don't work on ie8 (ES5)
              console.log('fin');
              request = false;
            });   
        },
           
        albumIsLoaded = function(id){
            var rt = false;
            for(var i = 0; i<albums.length; i++){
                if(albums[i].hasOwnProperty(id)){
                    if(Object.keys(albums[i][id]).length !== 0) rt = albums[i][id];
                    //if (albums[i][id].length == 0) rt = albums[i][id];
                }
            }
            return rt;
        },
        
        getPoster = function (id){
            var rt = 'posterblnk.gif';
            for(var i = 0; i<albums.length; i++){
                if(albums[i].hasOwnProperty(id)){
                    rt = albums[i]['poster'];
                }
            }
            return rt;
        },
        
        audioEnded = function(e ,t, ct){
            if(autoplay){
                curTracks ++;
                if(curTracks < totTracks){
                    var id = audioTag.getAttribute('data-currentId');
                    var datas = albumIsLoaded(id);
                    audioTag.pause();
                    audioTag.src = datas.tracks.items[curTracks].preview_url;
                    curSongId = 'tr-' + datas.tracks.items[curTracks].id; 
                    var newt = $ID('sliding').childNodes[curTracks].childNodes[0].getAttribute('id');
                    var oldt = $ID('sliding').childNodes[curTracks-1].childNodes[0].getAttribute('id');
                    removeAclass(oldt,'currenttrack');
                    addAclass(newt,'currenttrack');
                    audioTag.play();
                }
                if(curTracks == totTracks){
                    var lastt = $ID('sliding').childNodes[totTracks-1].childNodes[0].getAttribute('id');
                    removeAclass(lastt,'currenttrack');
                    manageEvents.listenerRemove('audio','ended',audioEnded);
                }
            }else{
                manageEvents.listenerRemove('audio','ended',audioEnded);
                removeAclass(curSongId,'currenttrack');
            }
        },
        
        //getAlbum = function(e,t){
        getAlbum = function(id){
            //var id = t.getAttribute('data-album');
            var datas = albumIsLoaded(id);
            audioTag.setAttribute('data-currentId',id);
            if( datas == false){
                var datas = 'action=playlist&url=https://api.spotify.com/v1/albums/' + id + '&token='+token;
                if(request === false){
                    request = true;
                    getRefOnSPTF('_ajax/moanajax.php', datas, displayAlbum, id, autoplay);
                }
            }else{
                displayAlbum(datas,id);
            }
            
            //var audio = document.getElementById('audio');
            /*
            getRefOnSPTF('_ajax/ajax.php', datas, function(){
                // var audio = document.getElementById('audio');
                // audio.play();
            });
            */
            //if(autoplay) audioTag.play();
            return false;
        },
        
        playTrack = function(e,t,ct){
            autoplay = false;
            manageEvents.listenerRemove('audio','ended',audioEnded);
            var urltrack = t.getAttribute('data-track');
            var id = t.getAttribute('id');
            // reinit toutes les pistes
            for( var i=0; i < t.parentNode.parentNode.childNodes.length; i++){
                var a =  t.parentNode.parentNode.childNodes[i].childNodes[0].getAttribute('id');
                if(hasAclass(a,'currenttrack')) removeAclass(a,'currenttrack');
            }
            if(id == curSongId){ // && hasAclass(id,'currenttrack')
                curSongId = 0;
                removeAclass(id,'currenttrack');
                audioTag.pause();
                audioTag.currentTime = 0;
            }else{
                addAclass(id,'currenttrack');
                curSongId = id;
                audioTag.src = urltrack;
                audioTag.play();
            }
            return false;
        },
        
        displayAlbum = function(data,id){
            
            var imgposter = getPoster(id);
            $ID('posters').src = '_img/' + lang + '/posters/'+imgposter;
            
            if(data.tracks.items){
                var elems = '';
                curTracks = 0;
                totTracks = data.tracks.items.length;
                //var imageurl = data.images[(data.images.length-1)];
                var imageurl = data.images[1].url;
                var frstid = 'tr-' + data.tracks.items[0].id;
                for(var i=0; i<totTracks;i++){
                    var item = data.tracks.items[i];
                    var ms = item.duration_ms;
                    var min = Math.round((ms/1000/60) << 0);
                    var sec = Math.round((ms/1000) % 60);
                    //var secd = Math.round(sec);
                    sec = sec < 10 ? sec+'0' : sec;
                    var duree = min+':'+ sec;
                    //var namestr = item.name.replace(/\s/g, "&nbsp;");
                    //namestr = namestr.replace(/-/g, '-&#8288;');
                    var namestr = item.name.replace(/-/g, '-&#8288;');
                    elems += '<li><a href="#' + item.id +'" data-track="' + item.preview_url + '" id="tr-' + item.id + '" class="track"><span class="icons"><img src="_img/icon_blk.gif"/></span><span class="numtr">' + item.track_number + '</span><span class="nametr">' + namestr + '</span><span class="artisttr">' + item.artists[0].name + '</span><span class="dureetr">' + duree + '</span></a></li>';
                }
                var cont = $ID('album');
                cont.innerHTML = '';
                cont.innerHTML = '<div class="header"><div class="headimg"><img src="' + imageurl+ '" width="300" height="300"/></div><div class="titre">' + data.name + '</div></div>'; 
                cont.innerHTML += '<div class="liste" id="liste"><ul id="sliding">'+ elems + '</ul><div id="gutter" class="gutter novisible"><div class="slider" id="slider"></div></div></div>';
                
                for(var i=0; i<data.tracks.items.length;i++){
                    var item = data.tracks.items[i];
                    manageEvents.listenerAdd('tr-'+item.id,'click',playTrack,true);
                }
            
                                    
                if(autoplay){
                    if($ID('sliding') !== null) totTracks = $ID('sliding').childNodes.length;
                    curSongId = frstid; 
                    manageEvents.listenerAdd('audio','ended',audioEnded,false);
                }
                
                var track = data.tracks.items[0];
                var frstid = $ID('sliding').childNodes[0].childNodes[0].getAttribute('id');
                addAclass(frstid,'currenttrack');
                audioTag.src = track.preview_url;
                if(autoplay) audioTag.play();
                // reinit le Slider apres 1 seconde
                var testHeight = setTimeout(function(){
                    
                    initSlider();
                    
                    
                }, 600);
                
            }
            
        },
        
        slidetouchstart = function(e, t, ct){
            slider.touchstarty = e.touches[0].clientY;
            manageEvents.listenerAdd('liste','touchmove',slidetouchmove,true);
            manageEvents.listenerAdd('liste','touchend',slidetouchend,false);
            manageEvents.listenerAdd('liste','touchleave',slidetouchend,false);
            return false;
        },
            
        slidetouchmove = function(e, t, ct){
            var ytop = $ID('sliding').getBoundingClientRect().top;
            var ybott = $ID('sliding').getBoundingClientRect().bottom;
            
            /*
            if(ybott > slider.boundlist.bottom){
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)'
            }
            */
            
            if(e.touches[0].clientY < slider.touchstarty && ybott > slider.boundlist.bottom) {
                slider.ulY -= slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
            }
            
            if(e.touches[0].clientY > slider.touchstarty && ytop < slider.boundlist.top) {
                slider.ulY += slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
            }
            
            if(ytop > slider.boundlist.top){
                slider.ulY = 0;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
            }
            
            slider.touchstarty = e.touches[0].clientY;

            return false;
        },
            
        slidetouchend = function(e, t, ct){
            manageEvents.listenerRemove('liste','touchmove');
            manageEvents.listenerRemove('liste','touchend');
            manageEvents.listenerRemove('liste','touchleave');
        },
            
        slidewheelMouse = function(e, t, ct){
                //var delta = Math.max(-1, Math.min(1, (e.wheelDelta || -e.detail)));
            var delta = Math.max(-1, Math.min(1, e.deltaY));
            var ytop = $ID('sliding').getBoundingClientRect().top;
            var ybott = $ID('sliding').getBoundingClientRect().bottom;
            
            if(delta == -1 && ytop > slider.boundlist.top){
                slider.ulY = 0;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';
                return false;
            }
                
            if(delta == 1 && ybott <= slider.boundlist.bottom-slider.offYd){
                slider.ulY = -slider.deltalist;//slider.contH - slider.lstH;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';
                return false;
            }
            
            if(delta == 1 && ybott >= slider.boundlist.bottom) {
                slider.ulY -= slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';
            }
            
            if(delta == -1 && ytop < slider.boundlist.top) {
                slider.ulY += slider.offYd;
                $ID('sliding').style.transform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.transform = 'translate(0px,' + -slider.ulY + 'px)';
                // ie 9
                $ID('sliding').style.msTransform = 'translate(0px,' + slider.ulY + 'px)';
                $ID('slider').style.msTransform = 'translate(0px,' + -slider.ulY + 'px)';
            }
            
            return false;
        },
       
        // ^ fin gestion album et slider ----------------------------------------------------------------------------------------------------
        ```
