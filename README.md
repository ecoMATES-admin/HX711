Checkout README from original repor here: https://github.com/bogde/HX711

As the original implementation is blocking. This fork has been made to create an unblocking version of the lib.
```
long HX711::read() {

	// Wait for the chip to become ready.
	//wait_ready();
[...]
```
the blocking HX711:wait_ready()-method has been commented out of HX711:read().

The user has to check for readiness manually.

Before calling get_units(), tare(), read() or any other method that will end up calling read() call one of te two unblocking methods to check if chip is ready:

```
bool HX711::wait_ready_retry(int retries, unsigned long delay_ms) {
	// Wait for the chip to become ready by
	// retrying for a specified amount of attempts.
	// https://github.com/bogde/HX711/issues/76
	int count = 0;
	while (count < retries) {
		if (is_ready()) {
			return true;
		}
		delay(delay_ms);
		count++;
	}
	return false;
}
```
```
bool HX711::wait_ready_timeout(unsigned long timeout, unsigned long delay_ms) {
	// Wait for the chip to become ready until timeout.
	// https://github.com/bogde/HX711/pull/96
	unsigned long millisStarted = millis();
	while (millis() - millisStarted < timeout) {
		if (is_ready()) {
			return true;
		}
		delay(delay_ms);
	}
	return false;
}
```
WARNING!!!
If the readiness is not checked manually in advance, completely wrong values are returned.