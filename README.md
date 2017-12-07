# android-and-JavaScript-interactivity
This is Android and  JavaScript  interactivity articles
There are two ways for android to call JS code:
1. Through the web view's loadUrl ()
2. EvaluateJavascript () through WebView
There are three ways for JS to call Android code:
1. Object mapping through the addJavascriptInterface () of the WebView
2. Callback interceptor url through the shouldOverrideUrlLoading () method of the WebViewClient
3. Through the WebChromeClient onJsAlert (), onJsConfirm (), onJsPrompt () method callback interceptor JS dialog alert (), confirm (), prompt () message
Here is the mainactivity content
public class MainActivity extends AppCompatActivity {

    WebView mWebView;
    Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mWebView =(WebView) findViewById(R.id.webview);
        WebSettings webSettings = mWebView.getSettings();
        // set permissions to interact with Js
        webSettings.setJavaScriptEnabled(true);
        // Settings allow JS popovers
        webSettings.setJavaScriptCanOpenWindowsAutomatically(true);
        // load JS code first
        // the format is: file:///android_asset/ filename. HTML
        mWebView.loadUrl("file:///android_asset/javascript.html");
        button = (Button) findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // must have another thread to make the JS method call (otherwise it won't be called)
                mWebView.post(new Runnable() {
                    @Override
                    public void run() {
                       // notice that the JS method name of the call is corresponding
                       // call javascript's callJS() method
                       //mWebView.loadUrl("file html:funaction method")
                        mWebView.loadUrl("javascript:callJS()");
                    }
                });
            }
        });
        // because the popover test call result is set, the js dialog box needs to be supported
        // webview is just the carrier, and the rendering of the content needs to be implemented using the webviewChromClient class
        // by setting up the WebChromeClient object to handle the JavaScript dialog box
        // set the response js Alert() function
        mWebView.setWebChromeClient(new WebChromeClient() {
            @Override
            public boolean onJsAlert(WebView view, String url, String message, final JsResult result) {
                AlertDialog.Builder b = new AlertDialog.Builder(MainActivity.this);
                b.setTitle("Alert");
                b.setMessage(message);
                b.setPositiveButton(android.R.string.ok, new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        result.confirm();
                    }
                });
                b.setCancelable(false);
                b.create().show();
                return true;
            }
        });
    }
}
