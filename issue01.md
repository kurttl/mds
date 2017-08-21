# Issue in app-activation-launcher

## Issue:
https://fabric.io/tinklabs2/android/apps/com.tinklabs.activateapp/issues/5922fd39be077a4dcc12b39a?time=last-seven-days
**trying to requery an already closed cursor **

## Suggestions:
http://www.itgo.me/a/2521468704169684261/android-database-staledataexception-attempted-to-access-a-cursor-after-it-has-b

According to the link, it provides 2 suggestions to avoid this problem:
1. Function, managedQuery() is deprecated.  
   Please use getContentResolver().query().  
   The parameters is the same.  
2. Should close cursor in finally section,  
   and set cursor to null after closing it.

```
if (cursor != null) {
  try {
  ...
  } catch (Exception ex) {
  ...
  } finally {
    try {
      if (!cursor.isClosed()) {
        cursor.close(); 
      }
      cursor = null; 
    } catch (Exception e) {
      Log.e("While closing cursor", e); 
    }
  }
```

## Code Review:
#### 1.app/src/main/java/com/tinklabs/activateapp/commons/database/MorningCallAlarmDBHelper.java:

```
    public List<MorningCallAlarm> restoreAlarm (){ 
        List<MorningCallAlarm> items =  new ArrayList<MorningCallAlarm>();
        SQLiteDatabase db = this.getReadableDatabase();
        String selectQuery = "SELECT * FROM " + Table_MorningCallAlarm ;
        Cursor c = db.rawQuery(selectQuery, null); // <--- I think it's not related to activity
        if(c.moveToFirst())
        {   
            ...
        }   
        c.close();
        return items;
    }                  
```

#### [2.app/src/main/java/com/tinklabs/activateapp/commons/util/UsageDataUtils.java:](https://github.com/TinkLabs/app-activation-launcher/blob/master/app/src/main/java/com/tinklabs/activateapp/commons/util/UsageDataUtils.java#L259)
```
    @WorkerThread
    private static Long getTotalOnTimes() {
        Long totalSum = 0L; 
        Cursor cursor = ActivateApplication.getInstance().getContentResolver().query(Uri.parse(CONTENT_PROVIDER_SCREEN_ON_LOG_URI), null, null, null, null);
        if (cursor != null) {
            if (cursor.moveToFirst()) {
                ...
            }   
            cursor.close(); // <--- Should close cursor in finally section, and set cursor to null after closing it
        }   

        Log.d(TAG, "total sum: " + totalSum);
        return totalSum;
    } 
```

#### [3.app/src/main/java/com/tinklabs/activateapp/commons/util/dial/DialUtils.java:](https://github.com/TinkLabs/app-activation-launcher/blob/master/app/src/main/java/com/tinklabs/activateapp/commons/util/dial/DialUtils.java#L45)
```
    public List<Dial> getMissDialog(Context context) {
        ...
        Cursor cursor = context.getContentResolver().query(
                Uri.parse("content://call_log/calls"),
                projection,
                selection,
                selectionArgs,
                sortOrder);
        if (cursor != null) {
            try {
                ...
            } catch (Exception ex) {
                Log.w(ex);
            } finally {
                cursor.close();// <--- Should set cursor to null after closing it
            }   
        }   
        return dials;
    }
```

#### [4.app/src/main/java/com/tinklabs/activateapp/features/client/intro/oldflow/HandyIntroBaseFragment.java:](https://github.com/TinkLabs/app-activation-launcher/commit/08ddda37ba52ed5f68abe58a4b0b689b2f20456d)
```
    public void checkRingtones() {
        try {
            HandyShellUtils.getInstance().grantPermission(getActivity(), Manifest.permission.READ_EXTERNAL_STORAGE);
            final int size = new RingtoneManager(getActivity()).getCursor().getCount(); // Should detect getCursor() is null or isClosed()
            if (size < 1) {
                forceMediaScan();
            }
        } catch (Exception e) {
            Log.e(TAG, e); 
        }
    }
```

#### [5.handy-log/src/main/java/com/tinklabs/handy_log/FileUtils.java:](https://github.com/TinkLabs/handy-commons/blob/6141d12b0bd5ead3b3d873f4827faf070e736bc0/handy-log/src/main/java/com/tinklabs/handy_log/FileUtils.java#L189)
```
    public static String getDataColumn(Context context, Uri uri, String selection,
                                       String[] selectionArgs) {

        Cursor cursor = null;
        final String column = "_data";
        final String[] projection = { 
                column
        };  

        try {
            cursor = context.getContentResolver().query(uri, projection, selection, selectionArgs,
                    null);
            if (cursor != null && cursor.moveToFirst()) {
                final int column_index = cursor.getColumnIndexOrThrow(column);
                return cursor.getString(column_index);
            }   
        } finally {
            if (cursor != null) // <--- Should detect if cursor is not closed and set cursor to null after closing it
                cursor.close();
        }   
        return null;
    }
```

#### [6.handy-root/src/main/java/com/tinklabs/handy_root/impls/HandyRootImpl.java:](https://github.com/TinkLabs/handy-commons/blob/6141d12b0bd5ead3b3d873f4827faf070e736bc0/handy-root/src/main/java/com/tinklabs/handy_root/impls/HandyRootImpl.java#L409)
```
    private static void clearSimContacts(Context context) {
        Uri simUri = Uri.parse("content://icc/adn");
        Cursor simCursor = context.getContentResolver().query(simUri, null, null, null, null);

        if (simCursor != null) {
            while (simCursor.moveToNext()) {
                context.getContentResolver().delete(simUri, "tag='" + simCursor.getString(simCursor.getColumnIndex("name")) + "' AND " + "number='" + simCursor.getString(simCursor.getColumnIndex("number")) + "'", null);
            }
            simCursor.close(); // <--- Should close cursor in finally section, and set cursor to null after closing it
        }
    }
```

#### [7.handy-root/src/main/java/com/tinklabs/handy_root/impls/HandySystemImpl.java:](https://github.com/TinkLabs/handy-commons/blob/6141d12b0bd5ead3b3d873f4827faf070e736bc0/handy-root/src/main/java/com/tinklabs/handy_root/impls/HandySystemImpl.java#L58)
Thera are 7 more cursors needs to handle
```
    public String catSystemFile(Context context, String path) {
        String selection = "key = ?";
        String[] selectionArgs = {path};
        Cursor cursor = context.getContentResolver().query(URI, null, selection, selectionArgs, null);
        String ret = null;
        if (cursor != null) {
            if (cursor.moveToFirst())
                ret = cursor.getString(cursor.getColumnIndex("value"));
            cursor.close(); // <--- Should close cursor in finally section, and set cursor to null after closing it
        }
        return ret;
    }
```

#### [8.handy-util/src/main/java/com/tinklabs/handy_util/func/ImageUtils.java:](https://github.com/TinkLabs/handy-commons/blob/6141d12b0bd5ead3b3d873f4827faf070e736bc0/handy-util/src/main/java/com/tinklabs/handy_util/func/ImageUtils.java#L120)
```
    public static int getOrientation(Context context, Uri photoUri) {
        final int result;
        Cursor cursor = context.getContentResolver().query(
                photoUri, new String[]{MediaStore.Images.ImageColumns.ORIENTATION}, null, null, null
        );
        if (cursor == null) {
            return 0;
        }
        if (cursor.moveToFirst()) {
            result = cursor.getInt(0);
        } else {
            result = 0;
        }
        cursor.close(); // <--- Should close cursor in finally section, and set cursor to null after closing it
        return result;
    }
```
