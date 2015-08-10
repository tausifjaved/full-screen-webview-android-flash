public class FullscreenableChromeClient extends WebChromeClient {
> protected Activity mActivity = null;

> private View mCustomView;
> private WebChromeClient.CustomViewCallback mCustomViewCallback;
> private int mOriginalOrientation;

> private FrameLayout mContentView;
> private FrameLayout mFullscreenContainer;

> private static final FrameLayout.LayoutParams COVER\_SCREEN\_PARAMS = new FrameLayout.LayoutParams(ViewGroup.LayoutParams.MATCH\_PARENT, ViewGroup.LayoutParams.MATCH\_PARENT);

> public FullscreenableChromeClient(Activity activity) {
> > this.mActivity = activity;

> }

> @Override
> public void onShowCustomView(View view, int requestedOrientation, WebChromeClient.CustomViewCallback callback) {
> > if (Build.VERSION.SDK\_INT &gt;= Build.VERSION\_CODES.ICE\_CREAM\_SANDWICH) {
> > > if (mCustomView != null) {
> > > > callback.onCustomViewHidden();
> > > > return;

> > > }


> mOriginalOrientation = mActivity.getRequestedOrientation();
> FrameLayout decor = (FrameLayout) mActivity.getWindow().getDecorView();
> mFullscreenContainer = new FullscreenHolder(mActivity);
> mFullscreenContainer.addView(view, COVER\_SCREEN\_PARAMS);
> decor.addView(mFullscreenContainer, COVER\_SCREEN\_PARAMS);
> mCustomView = view;
> setFullscreen(true);
> mCustomViewCallback = callback;
> mActivity.setRequestedOrientation(requestedOrientation);
> }

> super.onShowCustomView(view, requestedOrientation, callback);
> }

> @Override
> public void onHideCustomView() {
> > if (mCustomView == null) {
> > > return;

> > }


> setFullscreen(false);
> FrameLayout decor = (FrameLayout) mActivity.getWindow().getDecorView();
> decor.removeView(mFullscreenContainer);
> mFullscreenContainer = null;
> mCustomView = null;
> mCustomViewCallback.onCustomViewHidden();
> mActivity.setRequestedOrientation(mOriginalOrientation);
> }

> private void setFullscreen(boolean enabled) {
> > Window win = mActivity.getWindow();
> > WindowManager.LayoutParams winParams = win.getAttributes();
> > final int bits = WindowManager.LayoutParams.FLAG\_FULLSCREEN;
> > if (enabled) {
> > > winParams.flags |= bits;

> > } else {
> > > winParams.flags &amp;= ~bits;
> > > if (mCustomView != null) {
> > > > mCustomView.setSystemUiVisibility(View.SYSTEM\_UI\_FLAG\_VISIBLE);

> > > } else {
> > > > mContentView.setSystemUiVisibility(View.SYSTEM\_UI\_FLAG\_VISIBLE);

> > > }

> > }
> > win.setAttributes(winParams);
> > }


> private static class FullscreenHolder extends FrameLayout {
> > public FullscreenHolder(Context ctx) {
> > > super(ctx);
> > > setBackgroundColor(ctx.getResources().getColor(android.R.color.black));

> > }


> @Override
> public boolean onTouchEvent(MotionEvent evt) {
> > return true;

> }
> }
}

source: http://www.techfreestyle.com/fixed-android-4-0-full-screen-flash-problem-in-webview