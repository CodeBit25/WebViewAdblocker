# WebViewAdblock

Android webview Adblocker is a simple library to block ads in webview. this code is basicaly stop the ads and remove the html from it and render it again. 

## How do I use it?
### Step 1

#1 Add it in your MainActivity.java
 

        AdBlocker.init(this);

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

Make a AdBlocker.java class and paste the below codes

    public class AdBlocker {

    private static final String AD_HOSTS_FILE = "host.txt";
    private static final Set<String> AD_HOSTS = new HashSet<>();

    public static void init(final Context context) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    loadFromAssets(context);
                } catch (IOException e) {
                    // noop
                }
                return null;
            }
        }.execute();
    }

    @WorkerThread
    private static void loadFromAssets(Context context) throws IOException {
        InputStream stream = context.getAssets().open(AD_HOSTS_FILE);
        InputStreamReader inputStreamReader = new InputStreamReader(stream);
        BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
        String line;
        while ((line = bufferedReader.readLine()) != null) AD_HOSTS.add(line);
        bufferedReader.close();
        inputStreamReader.close();
        stream.close();
    }

    public static boolean isAd(String url) {
        try {
            return isAdHost(getHost(url))||AD_HOSTS.contains(Uri.parse(url).getLastPathSegment());
        } catch (MalformedURLException e) {
            Log.d("Ind", e.toString());
            return false;
        }

    }

    private static boolean isAdHost(String host) {
        if (TextUtils.isEmpty(host)) {
            return false;
        }
        int index = host.indexOf(".");
        return index >= 0 && (AD_HOSTS.contains(host) ||
                index + 1 < host.length() && isAdHost(host.substring(index + 1)));
    }

    public static String getHost(String url) throws MalformedURLException {
        return new URL(url).getHost();
    }

    public static WebResourceResponse createEmptyResource() {
        return new WebResourceResponse("text/plain", "utf-8", new ByteArrayInputStream("".getBytes()));
    }
    }
	
### Step 4

Create a assests directory in main and add host.txt file into that assests directory.

 https://github.com/CodeBit25/WebViewAdblocker/blob/master/app/src/main/assets/host.txt

Now you can run your app to see if the ads are blocked. You can always use other webview features and add fetures to the webview.
