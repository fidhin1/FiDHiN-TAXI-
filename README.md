from twilio.rest import Client

# Twilio credentials (replace with your own)
account_sid = 'your_account_sid'
auth_token = 'your_auth_token'
twilio_number = '+1234567890'  # Your Twilio phone number

# Initialize Twilio client
client = Client(account_sid, auth_token)

# Function to send SMS
def send_sms(to_number, message):
    try:
        message = client.messages.create(
            body=message,
            from_=twilio_number,
            to=to_number
        )
        print(f"SMS sent to {to_number}: {message.body}")
    except Exception as e:
        print(f"Failed to send SMS: {e}")

# Taxi booking system
class TaxiService:
    def __init__(self):
        self.drivers = []
        self.bookings = []

    def add_driver(self, driver_name, phone_number):
        self.drivers.append({'name': driver_name, 'phone': phone_number})
        print(f"Driver {driver_name} added.")

    def book_taxi(self, customer_name, customer_phone, pickup_location, destination):
        if not self.drivers:
            print("No drivers available.")
            return

        driver = self.drivers.pop(0)  # Assign the first available driver
        booking = {
            'customer': customer_name,
            'customer_phone': customer_phone,
            'driver': driver['name'],
            'driver_phone': driver['phone'],
            'pickup': pickup_location,
            'destination': destination
        }
        self.bookings.append(booking)

        # Send SMS to customer
        send_sms(customer_phone, f"Your taxi is booked! Driver: {driver['name']}. Pickup: {pickup_location}.")

        # Send SMS to driver
        send_sms(driver['phone'], f"New booking! Customer: {customer_name}. Pickup: {pickup_location}.")

    def show_bookings(self):
        if not self.bookings:
            print("No bookings found.")
            return

        print("Current Bookings:")
        for booking in self.bookings:
            print(f"Customer: {booking['customer']}, Driver: {booking['driver']}, "
                  f"Pickup: {booking['pickup']}, Destination: {booking['destination']}")

# Example usage
if __name__ == "__main__":
    taxi_service = TaxiService()

    # Add drivers
    taxi_service.add_driver("Ali", "+1111111111")
    taxi_service.add_driver("Hassan", "+2222222222")

    # Book taxis
    taxi_service.book_taxi("Ahmed", "+3333333333", "Downtown", "Airport")
    taxi_service.book_taxi("Fatima", "+4444444444", "Mall", "Home")

    # Show all bookings
    taxi_service.show_bookings()