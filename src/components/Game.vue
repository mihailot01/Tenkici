<template>
    <div id="game"  @keydown="pritisnuo" @keyup="pustio" tabindex=-1>
    
        <canvas id="canvas" width="600" height="400" style="margin-bottom: 2rem; margin-top: 1rem" >

        <div style="display:none;">
        <img id="tenkic"
            src="@/assets/tenkic.png"
            width="50" height="50">
        <img id="tenkic2"
            src="@/assets/tenkic2.png"
            width="50" height="50">
        
        </div>

        </canvas>
        <div style="margin-top: 1rem;">
            <div style="float: left;">
                <img 
                src="@/assets/tenkicScore2.png"
                width="225" height="125">
                <h1 class="rez">{{score2}}</h1>
            </div>

            <div style="float: right;">
                <h1 class="rez">{{score}}</h1>
                <img 
                src="@/assets/tenkicScore.png"
                width="225" height="125">
            </div>
        </div>
    </div>
</template>

<script>
import fajl from '@/json/mapa.json'

export default {
    name: "Game",
    data: function(){
        return {
            dugme: 4,
            ctx: null,
            zidovi: [],
            vl: 1.5*1.414213562,
            loptice: [], 
            //tenkic: {img: null,x: 100,y:100, width:70, height:44, ugao: 0, dx:3*1.414213562, dy:0, okvir:[]},
            tajmer: null,
            deltate: 0,
            pritisnuti: [],
            score: 0,
            score2: 0,
            fajl: fajl,
            width: 0,
            height: 0
        }
    },
    methods: {
        drawTable(){
            this.ctx.beginPath();
            this.ctx.lineWidth = 6;
            //this.ctx.lineWidth = 5;
            for(var l of this.zidovi)
                this.drawLine(l.x1,l.y1,l.x2,l.y2);
            this.ctx.closePath();
        },
        drawLine(x1,y1,x2,y2){
            this.ctx.moveTo(x1, y1);
            this.ctx.lineTo(x2, y2);
            this.ctx.stroke();
        },
        drawBall(loptica){
            this.ctx.lineWidth=2;
            this.ctx.beginPath();
            this.ctx.arc(loptica.x, loptica.y, 2, 0,  2*Math.PI);
            this.ctx.stroke(); 
            this.ctx.closePath();
        },
        drawTank(tenkic){
            if(!tenkic.ziv)
                return;
            this.ctx.save();
            this.ctx.translate(tenkic.x,tenkic.y);
            this.ctx.rotate(tenkic.ugao);
            this.ctx.drawImage(tenkic.img,-tenkic.width/2,-tenkic.height/2,tenkic.width,tenkic.height);
            this.ctx.restore();
        },
        toLine(x1,y1,x2,y2){
            return { x1: x1, y1: y1, x2: x2, y2: y2};
        },
        vert(zid){
            return (zid.x1==zid.x2);
        },
        odbijanje(loptica){
            for(var l of this.zidovi){
                if(this.vert(l)){
                    if(Math.abs(loptica.x-l.x1)<=3 && (Math.min(l.y1,l.y2)<=loptica.y && Math.max(l.y1,l.y2)>=loptica.y)){
                        loptica.dx*=-1;
                        return;
                    }
                }
                else{
                    if(Math.abs(loptica.y-l.y1)<=3 && (Math.min(l.x1,l.x2)<=loptica.x && Math.max(l.x1,l.x2)>=loptica.x)){
                        loptica.dy*=-1;
                        return;
                    }
                }
            }
        },
        ispaliLopticu(tenkic, ind){
            var loptice2=this.loptice.filter(loptica => loptica.tenk==ind);
            if(loptice2.length<5 && (loptice2.length==0 || loptice2[loptice2.length-1].t>=15))
            {
                //console.log("USO",this.loptice.length);
                var loptica={};
                loptica.x=tenkic.x-(tenkic.width/2+2)*Math.cos(tenkic.ugao);
                loptica.y=tenkic.y-(tenkic.width/2+2)*Math.sin(tenkic.ugao);
                loptica.dx=-Math.cos(tenkic.ugao)*this.vl;
                loptica.dy=-Math.sin(tenkic.ugao)*this.vl;
                loptica.t=0;
                loptica.tenk=ind;
                this.loptice.push(loptica);

                this.odbijanje(loptica);
                loptica.x+=loptica.dx;
                loptica.y+=loptica.dy;
            }
        },
        pomeriTenkic(tenkic, p){
            tenkic.dx=-Math.cos(tenkic.ugao)*this.vl;
            tenkic.dy=-Math.sin(tenkic.ugao)*this.vl;
            tenkic.x+=p*tenkic.dx;
            tenkic.y+=p*tenkic.dy;
            if(this.udario(tenkic))  this.pomeriTenkic(tenkic, -p);
        },
        pritisnuo: function(event){
            this.pritisnuti[event.keyCode]=true;
            this.dugme=event.keyCode;
        },
        pustio: function(event){
            this.pritisnuti[event.keyCode]=false;
            this.dugme="nista";
        },
        proizvod(x1, y1, x2, y2){
            return x1*y2-x2*y1;
        },
        razl(a, b){
            if(a<=0 && b<=0)
                return false;
            if(a>=0 && b>=0)
                return false;
            return true;
        },
        sece(duz, tenkic){

            var xa=duz.x1+tenkic.x;
            var ya=duz.y1+tenkic.y;
            var xb=duz.x2+tenkic.x;
            var yb=duz.y2+tenkic.y;

            for(var zid of this.zidovi){
                var xc=zid.x1;
                var yc=zid.y1;
                var xd=zid.x2;
                var yd=zid.y2;
                var vp1=this.proizvod(xb-xa,yb-ya,xc-xa,yc-ya);
                var vp2=this.proizvod(xb-xa,yb-ya,xd-xa,yd-ya);

                var vp3=this.proizvod(xd-xc,yd-yc,xa-xc,ya-yc);
                var vp4=this.proizvod(xd-xc,yd-yc,xb-xc,yb-yc);

                if(this.razl(vp1,vp2) && this.razl(vp3,vp4))
                    return true;
            }
            return false;
        },
        sece2(duz,tenkic, loptica){
            var xa=duz.x1+tenkic.x;
            var ya=duz.y1+tenkic.y;
            var xb=duz.x2+tenkic.x;
            var yb=duz.y2+tenkic.y;

            var xc=0;
            var yc=0;
            var xd=loptica.x;
            var yd=loptica.y;
            
            var vp1=this.proizvod(xb-xa,yb-ya,xc-xa,yc-ya);
            var vp2=this.proizvod(xb-xa,yb-ya,xd-xa,yd-ya);

            var vp3=this.proizvod(xd-xc,yd-yc,xa-xc,ya-yc);
            var vp4=this.proizvod(xd-xc,yd-yc,xb-xc,yb-yc);

            if(this.razl(vp1,vp2) && this.razl(vp3,vp4))
                return true;
        
            return false;
        },
        udario(tenkic){
            for(var l of tenkic.okvir)
                if(this.sece(l, tenkic))
                    return true;
            return false;      
        },
        uTenku(tenkic, loptica){
            if(!tenkic.ziv)
                return;
            var brPreseka=0;
            for(var l of tenkic.okvir)
                if(this.sece2(l, tenkic, loptica))
                    brPreseka++;
            if(brPreseka==1)
                return true;
            return false;
        },
        rotiraj(tenkic, z){
            tenkic.ugao+=z*Math.PI/56;                
            //console.log(this.tenkic.ugao);

            for(var line of tenkic.okvir){
                var dx=line.x1
                var dy=line.y1
                var d=Math.sqrt(dx*dx+dy*dy);
                var fi = Math.atan2(dy,dx);
                //console.log(line, fi);
                fi+=z*Math.PI/56; 
                dx=Math.cos(fi)*d;
                dy=Math.sin(fi)*d;
                line.x1=dx;
                line.y1=dy;
                
                dx=line.x2
                dy=line.y2
                d=Math.sqrt(dx*dx+dy*dy);
                fi = Math.atan2(dy,dx);
                fi+=z*Math.PI/56; 
                dx=Math.cos(fi)*d;
                dy=Math.sin(fi)*d;
                line.x2=dx;
                line.y2=dy;
            }
            if(this.udario(tenkic)) this.rotiraj(tenkic, -z);
        },
        pomeri(){
            if(this.deltate>=200){ 
                if(this.tenkici[0].ziv)
                    this.score++;
                     
                if(this.tenkici[1].ziv)
                    this.score2++;
                clearInterval(this.tajmer);
                this.postavi();
                return;
            }
            
            if(!this.tenkici[0].ziv || !this.tenkici[1].ziv)
                this.deltate++;
            
            if(this.tenkici[0].ziv){
                //console.log("ZIV SAAAAM 0");
                if(this.pritisnuti[39]) this.rotiraj(this.tenkici[0], 1);
                if(this.pritisnuti[37]) this.rotiraj(this.tenkici[0], -1);
                if(this.pritisnuti[77]) this.ispaliLopticu(this.tenkici[0], 0);
                if(this.pritisnuti[38]) this.pomeriTenkic(this.tenkici[0], 1);
                if(this.pritisnuti[40]) this.pomeriTenkic(this.tenkici[0], -1);
            } 

            if(this.tenkici[1].ziv){   
                if(this.pritisnuti[70]) this.rotiraj(this.tenkici[1], 1);
                if(this.pritisnuti[83]) this.rotiraj(this.tenkici[1], -1);
                if(this.pritisnuti[81]) this.ispaliLopticu(this.tenkici[1], 1);
                if(this.pritisnuti[69]) this.pomeriTenkic(this.tenkici[1], 1);
                if(this.pritisnuti[68]) this.pomeriTenkic(this.tenkici[1], -1);
            }
            this.ctx.clearRect(0, 0, this.width, this.height);

            for(var loptica of this.loptice)
            {
                if(loptica.t>=800)
                    this.loptice.splice(0,1);
                loptica.t++;
                for(var t of this.tenkici)
                    if(this.uTenku(t, loptica)) { t.ziv=false; var ind=this.loptice.indexOf(loptica); this.loptice.splice(ind,1);}
                this.odbijanje(loptica);
                loptica.x+=loptica.dx;
                loptica.y+=loptica.dy;
                this.drawBall(loptica);
            }

            this.drawTank(this.tenkici[0]);
            this.drawTank(this.tenkici[1]);
            this.iscrtajOkvir(this.tenkici[0]);
            this.drawTable();

        },
        iscrtajOkvir(tenkic){
            this.ctx.lineWidth=1;
            this.ctx.beginPath();
            for(var d of tenkic.okvir)
                this.drawLine(d.x1+tenkic.x,d.y1+tenkic.y,d.x2+tenkic.x,d.y2+tenkic.y);
            this.ctx.closePath();
        }, 
        postavi(){
            var mapa=this.fajl[Math.floor(Math.random() * this.fajl.length)];
            this.deltate=0;
            this.loptice=[];
            this.tenkici=[];
            this.zidovi=[];
            this.tenkici.push({img: null,x: mapa.tenkic1.x,y:mapa.tenkic1.y, width:46.66, height:29.33, ugao: mapa.tenkic1.ugao, dx:this.vl*1.414213562, dy:0, okvir:[], ziv: true});
            this.tenkici.push({img: null,x: mapa.tenkic2.x,y:mapa.tenkic2.y, width:46.66, height:29.33, ugao: mapa.tenkic2.ugao, dx:this.vl*1.414213562, dy:0, okvir:[], ziv: true});
            for(var z of mapa.zidovi)
                this.zidovi.push(this.toLine(z[0],z[1],z[2],z[3]));
            /*this.zidovi=[
                this.toLine(0,3,600,3),
                this.toLine(3,0,3,400),
                this.toLine(0,397,600,397),
                this.toLine(597,0,597,400),
                this.toLine(300,0,300,200),
                this.toLine(400,100,400,300),
                this.toLine(150,150,150,300)];*/
            for(var tenkic of this.tenkici)
            {
                tenkic.okvir=[
                    this.toLine(-0.5,-0.125,-0.35,-0.125),
                    this.toLine(-0.35,-0.125,-0.35,-0.5),
                    this.toLine(-0.35,-0.5, 0.5,-0.5),
                    this.toLine(0.5,-0.5, 0.5,0.5),
                    this.toLine(0.5,0.5,-0.35,0.5),
                    this.toLine(-0.35,0.5,-0.35,0.125),
                    this.toLine(-0.35,0.125,-0.5,0.125),
                    this.toLine(-0.5,0.125,-0.5,-0.125)
                ];
            
                for(var d of tenkic.okvir){
                    d.x1*=tenkic.width; d.x2*=tenkic.width;
                    d.y1*=tenkic.height; d.y2*=tenkic.height;
                }
            }
            this.tenkici[0].img=document.getElementById("tenkic");
            this.tenkici[1].img=document.getElementById("tenkic2");

            var c = document.getElementById("canvas");
            this.width=mapa.width;
            this.height=mapa.height;
            c.width=mapa.width;
            c.height=mapa.height;
            var ctx2 = c.getContext("2d");    
            this.ctx = ctx2;
                        this.ctx.clearRect(0, 0, this.width, this.height);

            //this.drawTable();
            //this.drawBall();
            this.tajmer=setInterval(this.pomeri,12.5);
        },
    },
    mounted() {
        //console.log(this.mapa);
        window.addEventListener("keydown", function(e) {
            // space and arrow keys
            if([32, 37, 38, 39, 40].indexOf(e.keyCode) > -1) {
                e.preventDefault();
            }
        }, false);
        this.postavi();  
    },
}
</script>




<style scoped>
    #game{
        background-color: white;
    }
    #canvas{
        background-color: whitesmoke;
        margin-bottom: 4rem;
    }
    .rez{
        display: inline-block;
        margin: 4rem;
        margin-bottom: 0;
    }
</style>