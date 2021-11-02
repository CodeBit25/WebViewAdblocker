# WebViewAdblock

Android webview Adblocker is a simple library to block ads in webview. this code is basicaly stop the ads and remove the html from it and render it again. 

## How do I use it?
### Step 1

#1 Add it in your MainActivity.java
 
webview=(WebView)findViewById(R.id.webView);
        webview.setWebViewClient(new MyBrowser());

        WebSettings webSettings = webview.getSettings();
        webSettings.setJavaScriptEnabled(true);

        webview.loadUrl("https://www.meersworld.net/");
 
 ### Step 2
 
 Add these codes in MainActivity.java
 
private class MyBrowser extends WebViewClient {
        @Override
        public boolean shouldOverrideUrlLoading(WebView view, String url) {
            view.loadUrl(url);
            return true;
        }

        private Map<String, Boolean> loadedUrls = new HashMap<>();
        @Nullable
        @Override
        public WebResourceResponse shouldInterceptRequest(WebView view, String url) {
            boolean ad;
            if (!loadedUrls.containsKey(url)) {
                ad = AdBlocker.isAd(url);
                loadedUrls.put(url, ad);
            } else {
                ad = loadedUrls.get(url);
            }
            return ad ? AdBlocker.createEmptyResource() :
                    super.shouldInterceptRequest(view, url);
        }
    }
	
### Step 3

Create a assest directory in main and add host.txt file into assest directory.



Now you can run your app to see if the ads are blocked. You can always use other webview features and add fetures to the webview.
