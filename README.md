# Web-App-
Activity 2
from django.db import models
from django.contrib.auth.models import User


class Customer(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    email = models.EmailField()
    phone_number = models.CharField(max_length=20)
    address = models.CharField(max_length=200)

    def __str__(self):
        return f"{self.first_name} {self.last_name}"


class Room(models.Model):
    room_number = models.CharField(max_length=10, unique=True)
    floor = models.PositiveIntegerField()
    beds = models.PositiveIntegerField()
    price_per_night = models.DecimalField(max_digits=8, decimal_places=2)
    description = models.TextField()

    def __str__(self):
        return f"Room {self.room_number}"


class Reservation(models.Model):
    customer = models.ForeignKey(Customer, on_delete=models.CASCADE)
    room = models.ForeignKey(Room, on_delete=models.CASCADE)
    check_in = models.DateField()
    check_out = models.DateField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return f"{self.customer} - {self.room}"


class Booking(models.Model):
    reservation = models.OneToOneField(Reservation, on_delete=models.CASCADE)
    is_paid = models.BooleanField(default=False)
    payment_method = models.CharField(max_length=50, null=True, blank=True)

    def __str__(self):
        return f"{self.reservation} - {'Paid' if self.is_paid else 'Not paid'}"


class Staff(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    phone_number = models.CharField(max_length=20)

    def __str__(self):
        return f"{self.user.username}"
        
        In the above code, we have created five Model classes:

Customer: represents a customer who can make a reservation for a room. It has fields for first name, last name, email, phone number, and address.

Room: represents a room available for booking. It has fields for room number, floor, number of beds, price per night, and a description.

Reservation: represents a reservation made by a customer for a specific room for a specific time period. It has a foreign key to the Customer and Room models, as well as fields for check-in and check-out dates and audit fields for created_at and updated_at.

Booking: represents a booking made for a reservation, which includes payment information. It has a one-to-one relationship with Reservation and fields for whether the booking has been paid and the payment method.

Staff: represents a staff member who can manage reservations and bookings. It has a one-to-one relationship with Django's built-in User model and a phone number field.

To add audit fields to the models, we can use the DateTimeField with auto_now_add=True for the created_at field and auto_now=True for the updated_at field in the Reservation model.

To override the delete method and disable it for non-staff members, we can define a custom delete method in the Reservation model that checks if the user making the request is a staff member. If they are, the reservation can be deleted. If not, an exception is raised.

python
Copy code
from django.core.exceptions import PermissionDenied


class Reservation(models.Model):
    customer = models.ForeignKey(Customer, on_delete
