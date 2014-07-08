# Forked! Active developement is now at https://github.com/jpetrocik/bluetoothserial

# Android Bluetooth Serial 

Class wraps all the Android bluetooth internals needed to establish and maintain a bidirectional serial communication with bluetooth.  Uses a callback to send data receive via bluetooth to your activity.  You just need to implement the BluetoothSerial.MessageHanler callback.  The example below does it using an anonymous inner class.

Connections are automatically reestablished if the connection is lost.  Add LocalBroadcastManager registerReceiver to your activity to be notified when the connection is lost and/or reestablished.

When the bluetooth slave device moves out of range the BluetoothSerial class automatically attempts to reconnects.  After 30 failed attempts the class sends a BLUETOOTH_FAILED message.  To reconnect when the device is in range call BluetoothSerial.connect().
  
Originally developed to work with the HC-06 and HC-05 bluetooth modules found on ebay.  However any Serial Port Profile (SPP) bluetooth device should work.

Due to the nature of serial communication there is nothing to ensure the complete sequence of bytes has arrived.  So it is best to structure your communication protocol in such a way that you know when all the bytes of a message have arrived, there are some good tutorials on serial protocol formats on the internet.  The BluetoothSerial class handles buffering bytes until the complete message arrives. This is the reason you must return the number of byte read in the MessageHandler.read method. In the read method write your logic to consumer the bytes from the buffer, if the message is incomplete just return 0.  When read is called again after move data arrives check again to see if all data has arrived and then consume the bytes.  Return the number of bytes consumed and BluetoothSerial will buffer the un-consumed bytes until the remaining bytes arrive.  To ignore bytes in the buffer return the number of bytes to skip and the buffer will drop those bytes.  


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


## Send data

There are 3 different write methods are exposed, all the underlining SerialOutputStream write methods.


		bluetoothSerial.write();

		
## register receivers

Receivers are used to receive connection events.  Often this is used to update the UI. 

		//Fired when connection is established and also fired when onResume is called if a connection is already established. 
		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothConnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_CONNECTED));
		//Fired when the connection is lost
		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothDisconnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_DISCONNECTED));
		//Fired when connection can not be established, after 30 attempts.
		LocalBroadcastManager.getInstance(this).registerReceiver(bluetoothDisconnectReceiver, new IntentFilter(BluetoothSerial.BLUETOOTH_FAILED));

