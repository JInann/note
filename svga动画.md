svga动画

文档地址  https://github.com/svga/SVGAPlayer-Web/blob/master/README.zh.md

```javascript
import SVGA from "svgaplayerweb";
const animationStore = new Proxy(
  {},
  {
    get(t, p) {
      if (t[p]) return t[p];
      return (t[p] = new Promise((resolve, reject) => {
        var parser = new SVGA.Parser();
        parser.load(
          p,
          function(videoItem) {
            resolve(videoItem);
          },
          reject
        );
      }));
    }
  }
);
export default {
  bind(el, { value }) {
    var player = new SVGA.Player(el);
    animationStore[value.path || value].then(videoItem => {
      player.setVideoItem(videoItem);
      player.loops = 1;
      player.startAnimation();
      player.onFinished(() => {
        let e = new Event("finished");
        el.dispatchEvent(e);
        value.onFinished && value.onFinished();
      });
    });
  }
};
export const load = path => {
  console.log("load", animationStore[path]);
};

```

