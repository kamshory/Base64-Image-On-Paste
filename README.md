# Base64-Image-On-Paste

When copy-pasting HTML imagery from a website and you want all images to be base64-coded URLs so that users don't have to rely on the original website anymore, the browser will fetch all image URLs in the copied document. The browser will then replace all image URLs with base64-encoded data.

```js
var editor = document.querySelector('.editor');
        editor.addEventListener("paste", function(e) {
            // cancel paste
            e.preventDefault();

            var text = (e.originalEvent || e).clipboardData.getData('text/plain');
            var html = (e.originalEvent || e).clipboardData.getData('text/html');

            var doc = new DOMParser().parseFromString(html, "text/html");

            var images = doc.getElementsByTagName('img');
            var imgs = [];
            for(var i = 0; i<images.length; i++)
            {
                imgs[i] = new Image();
                imgs[i].setAttribute('crossOrigin', 'anonymous');
                imgs[i].onload = function(){
                    var canvas = document.createElement("canvas");
                    canvas.width = this.width;
                    canvas.height = this.height;
                    var ctx = canvas.getContext("2d");
                    ctx.drawImage(this, 0, 0);
                    var dataURL = canvas.toDataURL("image/png");
                    editor.querySelector('img[src="'+this.src+'"]').setAttribute('src', dataURL)

                }
                imgs[i].src = images[i].src;
            }

            // insert text manually
            document.execCommand("insertHTML", false, html);
        });
 ```
