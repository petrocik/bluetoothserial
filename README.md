# Android Bluetooth Serial 

Class wraps all the Android bluetooth internals needed to establish and maintain a serial communication with bluetooth.  Uses a callback to send data receive via bluetooth to your activity.  You just need to implement the BluetoothSerial.MessageHanler callback.  The example below does it using an anonymous inner class.

Connections are automatically reestablished if the connection is lost.  Add LocalBroadcastManager registerReceiver to your activity to be notified when the connection is lost and/or reestablished.

When the bluetooth slave device moves out of range the BluetoothSerial class automatically attempts to reconnects.  After 30 failed attempts the class sends a BLUETOOTH_FAILED message.  To reconnect when the device is in range call BluetoothSerial.connect().
  

## Usage

### Activity onCreate method

		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);

			//MessageHandler is call when bytes are read from the serial input
			bluetoothSerial = new BluetoothSerial(this, new BluetoothSerial.MessageHandler() {
				@Override
				public int read(int bufferSize, byte[] buffer) {
					return doRead(bufferSize, buffer);
				}
			}, deviceNamePrefix);
			.
			.
			.
		}


## Activity onResume method

		protected void onResume() {
			super.onResume();

			//onResume calls connect, it is safe
			//to call connect even when already connected
			bluetoothSerial.onResume();
		}



## register receivers

Receivers are used to receive connection events.  Often this is used to update the UI. 

		//Fired when connection is established and also fired when onResume is called if a connection is already established. 
		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothConnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_CONNECTED));
		//Fired when the connection is lost
		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothDisconnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_DISCONNECTED));
		//Fired when connection can not be established, after 30 attempts.
		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothDisconnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_FAILED));

