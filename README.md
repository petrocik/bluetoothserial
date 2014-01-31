# Android Bluetooth Serial 

Class wraps all the Android bluetooth internals needed to establish and maintain a serial communication with bluetooth.  Uses a callback to send read data to your activity.  You just need to implement the BluetoothSerial.MessageHanler callback.  The example below does it using an anonymous inner class.

Connections are automatically reestablished if the connection is lost.  Adding registerReceiver your activity can be notified when the connection is lost and reestablished.

## Usage

### onCreate

		protected void onCreate(Bundle savedInstanceState) {
			super.onCreate(savedInstanceState);

			//MessageHandler is call when bytes are read from the serial input
			bluetoothSerial = new BluetoothSerial(this, new BluetoothSerial.MessageHandler() {
				@Override
				public int read(int bufferSize, byte[] buffer) {
					return doRead(bufferSize, buffer);
				}
			});
			.
			.
			.
		}


## onResume

		protected void onResume() {
			super.onResume();

			bluetoothSerial.onResume();
		}



## register recievers

		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothConnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_CONNECTED));
		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothDisconnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_DISCONNECTED));

