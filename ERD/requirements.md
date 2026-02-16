
create table users (
  user_id uuid primary key default gen_random_uuid (),
  first_name text not null,
  last_name text not null,
  email text unique not null,
  password_hash text not null,
  phone_number text,
  role text check (role in ('guest', 'host', 'admin')) not null,
  created_at timestamp default current_timestamp
);

create table properties (
  property_id uuid primary key default gen_random_uuid (),
  host_id uuid references users (user_id),
  name text not null,
  description text not null,
  location text not null,
  price_per_night numeric not null,
  created_at timestamp default current_timestamp,
  updated_at timestamp default current_timestamp
);

create table bookings (
  booking_id uuid primary key default gen_random_uuid (),
  property_id uuid references properties (property_id),
  user_id uuid references users (user_id),
  start_date date not null,
  end_date date not null,
  total_price numeric not null,
  status text check (status in ('pending', 'confirmed', 'canceled')) not null,
  created_at timestamp default current_timestamp
);

create table payments (
  payment_id uuid primary key default gen_random_uuid (),
  booking_id uuid references bookings (booking_id),
  amount numeric not null,
  payment_date timestamp default current_timestamp,
  payment_method text check (
    payment_method in ('credit_card', 'paypal', 'stripe')
  ) not null
);

create table reviews (
  review_id uuid primary key default gen_random_uuid (),
  property_id uuid references properties (property_id),
  user_id uuid references users (user_id),
  rating int check (
    rating >= 1
    and rating <= 5
  ) not null,
  comment text not null,
  created_at timestamp default current_timestamp
);

create table messages (
  message_id uuid primary key default gen_random_uuid (),
  sender_id uuid references users (user_id),
  recipient_id uuid references users (user_id),
  message_body text not null,
  sent_at timestamp default current_timestamp
);

create index idx_user_email on users using btree (email);

create index idx_property_id on properties using btree (property_id);

create index idx_booking_property_id on bookings using btree (property_id);

create index idx_booking_id on bookings using btree (booking_id);

create index idx_payment_booking_id on payments using btree (booking_id);
