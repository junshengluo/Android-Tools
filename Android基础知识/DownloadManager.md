DownloadManager
===

`DownloadManager`��`Android 2.3`�ṩ�������ࡣ���ܷǳ�ǿ����ȫû�б�Ҫ�Լ�ʵ�����ء�

һ��DownloadManager�򵥽��� 
---
`DownloadManager`��ϵͳ���Ÿ�������Ӧ��ʹ�õ��࣬����������̬�ڲ���`DownloadManager.Query`��`DownloadManager.Request`��**`DownloadManager.Request`��������һ�����أ�`DownloadManager.Query`������ѯ������Ϣ**��    

��Ҫ��������
```java
**public long enqueue(Request request)**    
ִ�����أ�����downloadId��downloadId�����ں����ѯ������Ϣ�������粻����������Sdcard�����С�������󲢷������쳣��ȴ����أ�������ֱ�����ء� 

**public int remove(long�� ids)**
ɾ�����أ���������ȡ�����ء���ͬʱɾ�������ļ��ͼ�¼�� 

**public Cursor query(Query query)**
��ѯ������Ϣ�� 

public static Long getRecommendedMaxBytesOverMobile(Context context)     
ͨ���ƶ��������ص�����ֽ���

public String getMimeTypeForDownloadedFile(long id)      
�õ����ص�mimeType��������ú������н���
 
������ͨ���鿴�������ǿ��Է��ֻ��и�CursorTranslator˽�о�̬�ڲ��ࡣ�������Ҫ��Query����һ�������DownloadProvider��DownloadManager֮������ӳ�䡣��DownloadProvider�е�ʮ����״̬��Ӧ����DownloadManager�е�����״̬��DownloadProvider�е�ʧ�ܡ���ͣԭ��ת��Ϊ��DownloadManager��ԭ��
```

����ʾ������
---
1. AndroidManifest�����Ȩ��
```xml
	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

2. ��ʼ����
```java
	DownloadManager downloadManager = (DownloadManager)getSystemService(DOWNLOAD_SERVICE);
	String apkUrl = "http://img.meilishuo.net/css/images/AndroidShare/Meilishuo_3.6.1_10006.apk";
	DownloadManager.Request request = new DownloadManager.Request(Uri.parse(apkUrl));
	request.setDestinationInExternalPublicDir("Trinea", "MeiLiShuo.apk");
	// request.setTitle("MeiLiShuo");
	// request.setDescription("MeiLiShuo desc");
	// request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
	// request.setAllowedNetworkTypes(DownloadManager.Request.NETWORK_WIFI);
	// request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_HIDDEN);
	// request.setMimeType("application/com.trinea.download.file");
	long downloadId = downloadManager.enqueue(request);
```
    �������downloadManager��enqueue�ӿڽ������أ�����Ψһ��downloadId��DownloadManager.Request���˹��캯����Uri�����⣬�������ö�Ϊ��ѡ���á�������������£�     
request.setDestinationInExternalPublicDir(��Trinea��,��MeiLiShuo.apk��);��ʾ�������ص�ַΪsd����Trinea�ļ��У��ļ���ΪMeiLiShuo.apk��

```java
setDestinationInExternalPublicDir��Դ��Ϊ��
public Request setDestinationInExternalPublicDir(String dirType, String subPath) {
	File file = Environment.getExternalStoragePublicDirectory(dirType);

	Xlog.v(XLOGTAG, "setExternalPublicDir: dirType " + 
			dirType + " subPath " + subPath + 
			"file" + file);

	if (file.exists()) {
		if (!file.isDirectory()) {
			throw new IllegalStateException(file.getAbsolutePath() +
					" already exists and is not a directory");
		}
	} else {
		if (!file.mkdir()) {
			throw new IllegalStateException("Unable to create directory: "+
					file.getAbsolutePath());
		}
	}
	setDestinationFromBase(file, subPath);
	return this;
}
```

��Դ�������ǿ��Կ�����������Ŀ¼ΪEnvironment.getExternalStoragePublicDirectory(dirType)������file��ͨ��file.mkdir()�����ģ���������ϼ�Ŀ¼�����ھͻ��½��ļ����쳣����������ǰ��������Լ�����File��mkdirs�����ݹ鴴����Ŀ¼�����£�

```java
File folder = new File(folderName);
return (folder.exists() && folder.isDirectory()) ? true : folder.mkdirs();
```
���򣬻ᱨ�쳣
������������·���ӿ�ΪsetDestinationUri��setDestinationInExternalFilesDir��setDestinationToSystemCache������setDestinationToSystemCache����ϵͳappʹ�á�

request.allowScanningByMediaScanner();��ʾ����MediaScannerɨ�赽����ļ���Ĭ�ϲ�����
request.setTitle(��MeiLiShuo��);����������֪ͨ����ʾ�ı���
request.setDescription(��MeiLiShuo desc��);����������֪ͨ����ʾ�Ľ���
request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
��ʾ���ؽ����к�������ɵ�֪ͨ���Ƿ���ʾ��Ĭ��ֻ��ʾ������֪ͨ��VISIBILITY_VISIBLE_NOTIFY_COMPLETED��ʾ������ɺ���ʾ֪ͨ����ʾ��VISIBILITY_HIDDEN��ʾ����ʾ�κ�֪ͨ����ʾ�������Ҫ��AndroidMainfest�����Ȩ��android.permission.DOWNLOAD_WITHOUT_NOTIFICATION.
 
request.setAllowedNetworkTypes(DownloadManager.Request.NETWORK_WIFI);
��ʾ����������������ͣ�Ĭ�����κ������¶��������ء���NETWORK_MOBILE��NETWORK_WIFI��NETWORK_BLUETOOTH���ּ�����Ͽɹ�ѡ�����ֻ����wifi���أ�����ǰ����Ϊ3g�������ػ�ȴ���
request.setAllowedOverRoaming(boolean allow)�ƶ�����������Ƿ��������Ρ�
 
request.setMimeType(��application/com.trinea.download.file��);
���������ļ���mineType����Ϊ���ع���Ui�е��ĳ������������ļ���������ɵ��֪ͨ����ʾ�������mimeTypeȥ���ļ����������ǿ�������������ԡ���������������mimeTypeΪapplication/com.trinea.download.file�����ǿ���ͬʱ����ĳ��Activity��intent-filterΪapplication/com.trinea.download.file��������Ӧ����Ĵ��ļ���

```xml
<intent-filter>
	<action android:name="android.intent.action.VIEW" />

	<category android:name="android.intent.category.DEFAULT" />

	<data android:mimeType="application/com.trinea.download.file" />
</intent-filter>
```

request.addRequestHeader(String header, String value)
����������ص��������ӵ�httpͷ������User-Agent��gzipѹ����

3. ���ؽ���״̬��������ѯ
```java
	class DownloadChangeObserver extends ContentObserver {

	public DownloadChangeObserver(){
		super(handler);
	}

	@Override
	public void onChange(boolean selfChange) {
		updateView();
	}

}

public void updateView() {
	int[] bytesAndStatus = downloadManagerPro.getBytesAndStatus(downloadId);
	handler.sendMessage(handler.obtainMessage(0, bytesAndStatus[0], bytesAndStatus[1],
											  bytesAndStatus[2]));
}

private DownloadChangeObserver downloadObserver;

@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.download_manager_demo);
	����
	downloadObserver = new DownloadChangeObserver();
}

@Override
protected void onResume() {
	super.onResume();
	/** observer download change **/
	getContentResolver().registerContentObserver(DownloadManagerPro.CONTENT_URI, true,
												 downloadObserver);
}

@Override
protected void onPause() {
	super.onPause();
	getContentResolver().unregisterContentObserver(downloadObserver);
}
```
�������ǻ����Uri.parse(��content://downloads/my_downloads��)��Ȼ���ѯ����״̬�ͽ��ȣ�����handler���и��£�handler�д���������ý�������״̬�ȡ�
����DownloadManagerPro.getBytesAndStatus����Ҫ�������£�

```java
public int[] getBytesAndStatus(long downloadId) {
	int[] bytesAndStatus = new int[] { -1, -1, 0 };
	DownloadManager.Query query = new DownloadManager.Query().setFilterById(downloadId);
	Cursor c = null;
	try {
		c = downloadManager.query(query);
		if (c != null && c.moveToFirst()) {
			bytesAndStatus[0] = c.getInt(c.getColumnIndexOrThrow(DownloadManager.COLUMN_BYTES_DOWNLOADED_SO_FAR));
			bytesAndStatus[1] = c.getInt(c.getColumnIndexOrThrow(DownloadManager.COLUMN_TOTAL_SIZE_BYTES));
			bytesAndStatus[2] = c.getInt(c.getColumnIndex(DownloadManager.COLUMN_STATUS));
		}
	} finally {
		if (c != null) {
			c.close();
		}
	}
	return bytesAndStatus;
}
```
�����������Կ���������Ҫ����DownloadManager.Query()���в�ѯ��DownloadManager.QueryΪ���ع�����⿪�ŵ���Ϣ��ѯ�࣬��Ҫ�������½ӿڣ�
setFilterById(long�� ids)��������id���й���
setFilterByStatus(int flags)��������״̬���й���
setOnlyIncludeVisibleInDownloadsUi(boolean value)�����Ƿ���download ui�пɼ����й��ˡ�
 
orderBy(String column, int direction)�����н������򣬲���Ŀǰ��֧��DownloadManager.COLUMN_LAST_MODIFIED_TIMESTAMP��DownloadManager.COLUMN_TOTAL_SIZE_BYTES����
 
4. ���سɹ�����
������ɺ����ع���ᷢ��DownloadManager.ACTION_DOWNLOAD_COMPLETE����㲥��������downloadId��Ϊ������ͨ�����ܹ㲥���ǿ��Դ򿪶�������ɵ����ݽ��в������������£�
```java
class CompleteReceiver extends BroadcastReceiver {

	@Override
	public void onReceive(Context context, Intent intent) {
		// get complete download id
		long completeDownloadId = intent.getLongExtra(DownloadManager.EXTRA_DOWNLOAD_ID, -1);
		// to do here
	}
};

private CompleteReceiver       completeReceiver;
@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.download_manager_demo);

	��
	completeReceiver = new CompleteReceiver();
	/** register download success broadcast **/
	registerReceiver(completeReceiver,
					 new IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE));
}

@Override
protected void onDestroy() {
	super.onDestroy();
	unregisterReceiver(completeReceiver);
}
```
5. ��Ӧ֪ͨ�����
(1) ��Ӧ������֪ͨ�����
���������֪ͨ����ʾ��ϵͳ������ص�Ӧ�õ�������ActionΪDownloadManager.ACTION_NOTIFICATION_CLICKED�㲥��intent.getDataΪcontent://downloads/all_downloads/29669�����һλΪdownloadId��
���ͬʱ���ض��Ӧ�ã�intent�����DownloadManager.EXTRA_NOTIFICATION_CLICK_DOWNLOAD_IDS���key����ʾ���صĵ�downloadId���顣������Ƶ����ع���֪ͨ������ʾ���ƣ�������һƪ������ܡ�

(2) ��Ӧ�������֪ͨ�����
��������������������д����������ǿ��Է���ϵͳ�����View action����mimeTypeȥ��ѯ�����Կ������������ڽ��ܵ�DownloadManager.Request��setMimeType������
```java
private void openDownload(Context context, Cursor cursor) {
	String filename = cursor.getString(cursor.getColumnIndexOrThrow(Downloads.Impl._DATA));
	String mimetype = cursor.getString(cursor.getColumnIndexOrThrow(Downloads.Impl.COLUMN_MIME_TYPE));
	Uri path = Uri.parse(filename);
	// If there is no scheme, then it must be a file
	if (path.getScheme() == null) {
		path = Uri.fromFile(new File(filename));
	}
	Intent activityIntent = new Intent(Intent.ACTION_VIEW);
	mimetype = DownloadDrmHelper.getOriginalMimeType(context, filename, mimetype);
	activityIntent.setDataAndType(path, mimetype);
	activityIntent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	try {
		context.startActivity(activityIntent);
	} catch (ActivityNotFoundException ex) {
		Log.d(Constants.TAG, "no activity for " + mimetype, ex);
	}
}
```
��������Ϲ���Ԫ����Ҫ���£������ٽϿ첻�ϵ�ִ��onChange���ҳ��������һ��Ӱ�졣�Ƽ�ScheduledExecutorService���ڲ�ѯ������:
```java
	public static ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(3);
Runnable command = new Runnable() {

		@Override
		public void run() {
			updateView();
		}
	};
scheduledExecutorService.scheduleAtFixedRate(command, 0, 3, TimeUnit.SECONDS);
```
��ʾ3�붨ʱˢ��

Android���ع���DownloadManager������չ��bug�޸�
===

������Ҫ��������޸�Androidϵͳ���ع�����֧�ָ���Ĺ��ܼ�����bug�޸ĺ���α�����Ч��Ŀǰ���ݰ�����ͣ���ء��������ء�֪ͨ����NotiExtra��NotiClass��wifi�л���3g�Զ���ͣ��Bug�޸ġ�
������Ҫ�޸ĵ�DownloadManager.java����Ŀ¼Ϊframeworks/base/core/java/android/app
DownloadInfo.java, DownloadProvider.java,DownloadThread.java�ļ�����Ŀ¼Ϊpackages/providers/DownloadProvider/src/com/android/providers/downloads
 
1����ͣ���������ع���
(1) DownloadProvider���޸�
```java
public int update(final Uri uri, final ContentValues values, final String where, final String[] whereArgs)
```
�������޸ĺ��������(ֻ������һ����Ч����)��
    if (Binder.getCallingPid() != Process.myPid()) {
		filteredValues = new ContentValues();
		copyString(Downloads.Impl.COLUMN_APP_DATA, values, filteredValues);
		copyInteger(Downloads.Impl.COLUMN_VISIBILITY, values, filteredValues);
		Integer i = values.getAsInteger(Downloads.Impl.COLUMN_CONTROL);
		if (i != null) {
			filteredValues.put(Downloads.Impl.COLUMN_CONTROL, i);
			startService = true;
		}

		// trinea BEGIN, added by trinea@trinea.cn 2013/03/01
		copyInteger(Downloads.Impl.COLUMN_STATUS, values, filteredValues);
		// trinea END
		copyInteger(Downloads.Impl.COLUMN_CONTROL, values, filteredValues);
		copyString(Downloads.Impl.COLUMN_TITLE, values, filteredValues);
		copyString(Downloads.Impl.COLUMN_MEDIAPROVIDER_URI, values, filteredValues);
		copyString(Downloads.Impl.COLUMN_DESCRIPTION, values, filteredValues);
		copyInteger(Downloads.Impl.COLUMN_DELETED, values, filteredValues);
    } else {

������// trinea BEGIN��ͷ��// trinea END��βΪ�޸Ĳ��֣��������ʾ��ͬ����ˡ���ΪDownloadProvider��ȫ���ԶԷǸý���id���޸Ļ���˵�COLUMN_STATUS״̬������������Ҫ��Ӹ��С�

(2) DownloadThread���޸�
	private void setupDestinationFile(State state, InnerState innerState)

���������ע�͵�һ��else if�����£�
	// trinea BEGIN, noted by trinea@trinea.cn 2013/03/01
	//} else if (mInfo.mETag == null && !mInfo.mNoIntegrity) {
	//    // This should've been caught upon failure
	//    if (Constants.LOGVV) {
	//        Log.d(TAG, "setupDestinationFile() unable to resume download, deleting "
	//                + state.mFilename);
	//    }
	//    f.delete();
	//    throw new StopRequestException(Downloads.Impl.STATUS_CANNOT_RESUME,
	//            "Trying to resume a download that can't be resumed");
	// trinea END
	
����һ�δ����ʾһ����֤���̣�����ȥ����
mETagΪ���ݿ��е�etag�ֶ�ֵ��������û�н��ͣ��о���һ����ֵ֤������hashcode��
mNoIntegrityΪ������no_integrity�ֶ�ֵ����ʾ�������ص�Ӧ�ó����ܷ���֤���ص��ļ��������ԡ������ӵ����Ƕ���etag��no_integrity��û���ṩ���õĽӿ�
 
(3) DownloadManager���������ͣ�ͼ����Ķ���ӿ�
/**
 * pause download, added by trinea@trinea.cn 2013/03/01
 * 
 * @param ids the IDs of the downloads to be paused
 * @return the number of downloads actually paused
 */
public int pauseDownload(long... ids) {
	if (ids == null || ids.length == 0) {
		// called with nothing to remove!
		throw new IllegalArgumentException("input param 'ids' can't be null");
	}

	ContentValues values = new ContentValues();
	values.put(Downloads.Impl.COLUMN_CONTROL, Downloads.Impl.CONTROL_PAUSED);
	values.put(Downloads.Impl.COLUMN_STATUS, Downloads.Impl.STATUS_PAUSED_BY_APP);
	if (ids.length == 1) {
		return mResolver.update(ContentUris.withAppendedId(mBaseUri, ids[0]), values,
				null, null);
	} 
	return mResolver.update(mBaseUri, values, getWhereClauseForIds(ids),
			getWhereArgsForIds(ids));
}

/**
 * resume download, added by trinea@trinea.cn 2013/03/01
 * 
 * @param ids the IDs of the downloads to be resumed
 * @return the number of downloads actually resumed
 */
public int resumeDownload(long... ids) {
	if (ids == null || ids.length == 0) {
		// called with nothing to remove!
		throw new IllegalArgumentException("input param 'ids' can't be null");
	}

	ContentValues values = new ContentValues();
	values.put(Downloads.Impl.COLUMN_CONTROL, Downloads.Impl.CONTROL_RUN);
	values.put(Downloads.Impl.COLUMN_STATUS, Downloads.Impl.STATUS_RUNNING);
	if (ids.length == 1) {
		return mResolver.update(ContentUris.withAppendedId(mBaseUri, ids[0]), values,
				null, null);
	} 
	return mResolver.update(mBaseUri, values, getWhereClauseForIds(ids),
			getWhereArgsForIds(ids));
}

��������ͣ���Ǽ������Ƕ���ͬʱ��Downloads.Impl.COLUMN_CONTROL��Downloads.Impl.COLUMN_STATUS�ֶν����޸ģ���Ϊ��DownloadInfo��private boolean isReadyToStart(long now)������,���COLUMN_CONTROL�ֶν����жϣ�������û��ֶ���ͣ�Ļ����ǲ����Զ������ģ����ִ������£�
```java
private boolean isReadyToStart(long now) {
	if (DownloadHandler.getInstance().hasDownloadInQueue(mId)) {
		// already running
		return false;
	}
	if (mControl == Downloads.Impl.CONTROL_PAUSED) {
		// the download is paused, so it's not going to start
		Xlog.i(Constants.DL_ENHANCE, "Download is paused " +
				"then no need to start");
		return false;
	}
	����
}
```

֮������ֱ�ӵ���DownloadManager��pauseDownload��resumeDownload�ӿڼ���
 
PS��Ҳ�������Բ����ڶ������޸ģ�������һ��DownloadProvider��update�����޸ı�Ϊ
	// trinea BEGIN, added by trinea@trinea.cn 2013/03/01
	copyInteger(Downloads.Impl.COLUMN_STATUS, values, filteredValues);
	copyInteger(Downloads.Impl.COLUMN_NO_INTEGRITY, values, filteredValues);
	// trinea END
	
�ڶ����޸ı�Ϊ��public int resumeDownload(long�� ids)����
```java
values.put(Downloads.Impl.COLUMN_CONTROL, Downloads.Impl.CONTROL_RUN);
values.put(Downloads.Impl.COLUMN_STATUS, Downloads.Impl.STATUS_RUNNING);
values.put(Downloads.Impl.COLUMN_NO_INTEGRITY, true);
```

2��֪ͨ����������NotiExtra��NotiClass
(1) DownloadProvider�����޸�private void checkInsertPermissions(ContentValues values)����
```java
values.remove(Downloads.Impl.COLUMN_IS_VISIBLE_IN_DOWNLOADS_UI);
values.remove(Downloads.Impl.COLUMN_MEDIA_SCANNED);
// BEGIN, added by trinea@trinea.cn 2013/03/01
values.remove(Downloads.Impl.COLUMN_NOTIFICATION_CLASS);
values.remove(Downloads.Impl.COLUMN_NOTIFICATION_EXTRAS);
// trinea END
```
��DownloadProvider insert֮ǰ�����checkInsertPermissions��鲻�ܱ�������ֶβ��룬����������Ҫ�����������ֶδ��ڡ�
 
(2) DownloadManager.Request��Ӷ���ӿ�
```java
// BEGIN, added by trinea@trinea.cn 2013/03/01
private CharSequence mNotiClass;
private CharSequence mNotiExtras;
// trinea END

/**
 * Set notiClass, to be used as destination when click downloading in download manager ui
 * 
 * @return this object
 */
public Request setNotiClass(CharSequence notiClass) {
	mNotiClass = notiClass;
	return this;
}

/**
 * Set notiExtras, to be sended to notiClass when click downloading in download manager ui
 * 
 * @return this object
 */
public Request setNotiExtras(CharSequence notiExtras) {
	mNotiExtras = notiExtras;
	return this;
}

ContentValues toContentValues(String packageName)��
	putIfNonNull(values, Downloads.Impl.COLUMN_TITLE, mTitle);
	putIfNonNull(values, Downloads.Impl.COLUMN_DESCRIPTION, mDescription);
	putIfNonNull(values, Downloads.Impl.COLUMN_MIME_TYPE, mMimeType);
	// trinea BEGIN
	putIfNonNull(values, Downloads.Impl.COLUMN_NOTIFICATION_CLASS, mNotiClass);
	putIfNonNull(values, Downloads.Impl.COLUMN_NOTIFICATION_EXTRAS, mNotiExtras);
	// trinea END
```java
��Request����ӽӿ��Լ������ֶ��޸ġ�ͨ����������NotiExtra��NotiClass�����ǿ��Ը�ϵͳ���ݸ��ḻ�Ĳ�������֪ͨ�������Ӧ����DownloadUi��ͨ��broadcast����Щ�������ݳ�������Ӧ�õ��á�

3��wifi�л���3g�Զ���ͣ
(1) �޸�DownloadInfo.java
```java
private int checkIsNetworkTypeAllowed(int networkType) {
	if (mIsPublicApi) {
		final int flag = translateNetworkTypeToApiFlag(networkType);
		final boolean allowAllNetworkTypes = mAllowedNetworkTypes == ~0;
		if (!allowAllNetworkTypes && (flag & mAllowedNetworkTypes) == 0) {
			return NETWORK_TYPE_DISALLOWED_BY_REQUESTOR;
		}
		// trinea BEGIN
		if (mStatus == Downloads.Impl.STATUS_WAITING_FOR_NETWORK 
				&& flag != DownloadManager.Request.NETWORK_WIFI) {
			return NETWORK_TYPE_DISALLOWED_BY_REQUESTOR;
		}
		// trinea END
	}
	return checkSizeAllowedForNetwork(networkType);
}
```
��ʾ�ȴ�����ʱʼ��ֻ�ȴ�wifi
 
(2) �޸�DownloadReceiver.java
```java
} else if (action.equals(ConnectivityManager.CONNECTIVITY_ACTION)) {
	NetworkInfo info = (NetworkInfo)
			intent.getParcelableExtra(ConnectivityManager.EXTRA_NETWORK_INFO);
	if (info != null && info.isConnected()) {
		startService(context);
	}
} else if (action.equals(Constants.ACTION_RETRY)) {
```
�޸�Ϊ��
```java
} else if (action.equals(ConnectivityManager.CONNECTIVITY_ACTION)) {
	NetworkInfo info = (NetworkInfo)
			intent.getParcelableExtra(ConnectivityManager.EXTRA_NETWORK_INFO);
	// trinea BEGIN
	/** 
	 * modified by trinea@trinea.cn @2013/04/01, resume download only when network type is wifi
	 */
	if (info != null && info.isConnected() && info.getType() == ConnectivityManager.TYPE_WIFI) {
		// trinea END
		startService(context);
	}
} else if (action.equals(Constants.ACTION_RETRY)) {
```
��ʾֻ������wifiʱ�Ż���serviceȥ����Ƿ�����

(3) �޸�DownloadThread.java
```java
/**
 * Check if the download has been paused or canceled, stopping the request appropriately if it
 * has been.
 */
private void checkPausedOrCanceled(State state) throws StopRequestException {
	synchronized (mInfo) {
		if (mInfo.mControl == Downloads.Impl.CONTROL_PAUSED) {
			Xlog.i(Constants.DL_ENHANCE, "DownloadThread: checkPausedOrCanceled: user pause download");
			throw new StopRequestException(
					Downloads.Impl.STATUS_PAUSED_BY_APP, "download paused by owner");
		}
		if (mInfo.mStatus == Downloads.Impl.STATUS_CANCELED) {
			throw new StopRequestException(Downloads.Impl.STATUS_CANCELED, "download canceled");
		}
	}

	// if policy has been changed, trigger connectivity check
	if (mPolicyDirty) {
		// trinea BEGIN
		/** 
		 * add by trinea@trinea.cn @2013/04/01, when switched from wifi to 3g, pause all download
		 */
		NetworkInfo info = mSystemFacade.getActiveNetworkInfo(mInfo.mUid);
		if (info != null && !info.isConnected()) {
			mInfo.mStatus = Downloads.Impl.STATUS_WAITING_FOR_NETWORK;
		}
		// trinea END
		checkConnectivity();
	}
}
```
��ʾ�������仯���ұ�ʾ����Ͽ�ʱ������״̬��Ϊ�ȴ����硣

4��Bug�޸�
(1) ���洢�ռ䲻��ʱ������DownloadManager���أ�ֻ��ʾ֪ͨ����ʾ�������ع���UI�в���ʾ
DownloadManager��Cursor runQuery(ContentResolver resolver, String[] projection, Uri baseUri)�����޸����£�
```java
if ((mStatusFlags & STATUS_RUNNING) != 0) {
	parts.add(statusClause("=", Downloads.Impl.STATUS_RUNNING));
	// trinea BEGIN
	parts.add(statusClause("=", Downloads.Impl.STATUS_INSUFFICIENT_SPACE_ERROR));
	// trinea END
}
```
DownloadManager��CursorTranslator���private int translateStatus(int status) �����޸����£�
```java
// trinea BEGIN
case Downloads.Impl.STATUS_INSUFFICIENT_SPACE_ERROR:
// trinea END
case Downloads.Impl.STATUS_RUNNING:
	return STATUS_RUNNING;
```

5�����밲װ
�޸ĺ�����Ҫ���±���ģ���ͬʱ����framweork��DownloadProvider��
framework��������Ϊ��./makeMtk model mm frameworks/base/core/
�����apk����·��Ϊout\target\product\model\system\framework\secondary_framework.jar��֮��push��system/framework�������ɡ�����������modelΪ���ͣ���mtkƽ̨����������ͬ
 
DownloadProvider��������Ϊ./makeMtk model mm packages/providers/DownloadProvider/
�����apk����·��Ϊout\target\product\model\system\app\DownloadProvider.apk��֮��push��system/app����(������Ҫ��ɾ��/system/app/Ŀ¼�µ�DownloadProvider.odex)