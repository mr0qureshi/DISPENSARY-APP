# DISPENSARY-APP
Flutter app for clinic management. Supports students, employees, doctors, &amp; receptionists with registration, login, doctor availability tracking, &amp; appointment booking. Built with Flutter, Dart, &amp; Provider. Clone, run flutter pub get, &amp; flutter run to start. Contributions welcome!
import 'package:flutter/material.dart';
import 'package:intl/intl.dart';
import 'package:provider/provider.dart';

// Doctor model
class Doctor {
  final String name;
  final String specialty;
  final String? designation;
  final bool isRegular;
  final String? timing;

  const Doctor({
    required this.name,
    required this.specialty,
    this.designation,
    required this.isRegular,
    this.timing,
  });
}

// State management for doctor availability and appointments
class ClinicState with ChangeNotifier {
  Map<String, bool> doctorAvailability = {};
  Map<String, List<Map<String, String>>> doctorAppointments = {};

  void initialize(List<Doctor> doctors) {
    final now = DateTime.now().toUtc().add(const Duration(hours: 5, minutes: 30)); // Convert to IST
    for (var doctor in doctors) {
      if (doctor.isRegular) {
        doctorAvailability[doctor.name] = true;
      } else {
        bool isAvailable = false;
        if (doctor.timing != null) {
          if (doctor.name == 'Dr. J. Sowmya') {
            isAvailable = (now.weekday == DateTime.monday || now.weekday == DateTime.thursday) &&
                now.hour >= 16 && now.hour < 18;
          } else if (doctor.name == 'Dr. B. Sandhya Rani') {
            final firstTuesday = DateTime(2025, 7, 1);
            final secondTuesday = DateTime(2025, 7, 8);
            isAvailable = (now.day == firstTuesday.day || now.day == secondTuesday.day) &&
                now.weekday == DateTime.tuesday &&
                (now.hour == 16 || (now.hour == 17 && now.minute < 30));
          } else if (doctor.name == 'Dr. Vasavi Reddy') {
            final thirdTuesday = DateTime(2025, 7, 15);
            final fourthTuesday = DateTime(2025, 7, 22);
            isAvailable = (now.day == thirdTuesday.day || now.day == fourthTuesday.day) &&
                now.weekday == DateTime.tuesday &&
                ((now.hour == 15 && now.minute >= 30) || now.hour == 16 || (now.hour == 17 && now.minute < 30));
          } else if (doctor.name == 'Dr. V. Hemanth Reddy') {
            final firstTuesday = DateTime(2025, 7, 1);
            final thirdTuesday = DateTime(2025, 7, 15);
            isAvailable = (now.day == firstTuesday.day || now.day == thirdTuesday.day) &&
                now.weekday == DateTime.tuesday &&
                now.hour >= 16 && now.hour < 18;
          } else if (doctor.name == 'Dr. B. Jagadeesh Babu') {
            isAvailable = now.weekday == DateTime.wednesday && now.hour == 17;
          } else if (doctor.name == 'Dr. Pradeep') {
            isAvailable = now.weekday == DateTime.wednesday && now.hour >= 17 && now.hour < 19;
          } else if (doctor.name == 'Dr. K. Karthik') {
            isAvailable = now.weekday == DateTime.thursday && now.hour >= 15 && now.hour < 17;
          } else if (doctor.name == 'Dr. G. Vidya Reddy') {
            isAvailable = now.weekday == DateTime.friday &&
                (now.hour == 16 || (now.hour == 17 && now.minute < 30));
          }
        }
        doctorAvailability[doctor.name] = isAvailable;
      }
    }
    notifyListeners();
  }

  void toggleAvailability(String doctorName, bool enable) {
    doctorAvailability[doctorName] = enable;
    notifyListeners();
  }

  void acceptAppointment(String doctorName, Map<String, String> appointment) {
    if (!doctorAppointments.containsKey(doctorName)) {
      doctorAppointments[doctorName] = [];
    }
    doctorAppointments[doctorName]!.add(appointment);
    notifyListeners();
  }
}

void main() {
  runApp(
    ChangeNotifierProvider(
      create: (context) => ClinicState(),
      child: const MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Sign In Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: const HomePage(),
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Welcome'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text(
              'Student',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 10),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const RegistrationForm(userType: 'Student'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Sign Up'),
                ),
                const SizedBox(width: 10),
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const LoginForm(userType: 'Student'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Login'),
                ),
              ],
            ),
            const SizedBox(height: 30),
            const Text(
              'Employee',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 10),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const RegistrationForm(userType: 'Employee'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Sign Up'),
                ),
                const SizedBox(width: 10),
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const LoginForm(userType: 'Employee'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Login'),
                ),
              ],
            ),
            const SizedBox(height: 30),
            const Text(
              'Doctor',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 10),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const RegistrationForm(userType: 'Doctor'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Sign Up'),
                ),
                const SizedBox(width: 10),
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const LoginForm(userType: 'Doctor'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Login'),
                ),
              ],
            ),
            const SizedBox(height: 30),
            const Text(
              'Reception',
              style: TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 10),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const RegistrationForm(userType: 'Reception'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Sign Up'),
                ),
                const SizedBox(width: 10),
                ElevatedButton(
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => const LoginForm(userType: 'Reception'),
                      ),
                    );
                  },
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(horizontal: 20, vertical: 15),
                    textStyle: const TextStyle(fontSize: 18),
                  ),
                  child: const Text('Login'),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }
}

class RegistrationForm extends StatefulWidget {
  final String userType;

  const RegistrationForm({super.key, required this.userType});

  @override
  State<RegistrationForm> createState() => _RegistrationFormState();
}

class _RegistrationFormState extends State<RegistrationForm> {
  final _formKey = GlobalKey<FormState>();
  final _nameController = TextEditingController();
  final _dobController = TextEditingController();
  final _rollNumberController = TextEditingController();
  final _hostelNameController = TextEditingController();
  final _roomNumberController = TextEditingController();
  final _bloodGroupController = TextEditingController();
  final _ageController = TextEditingController();
  final _sexController = TextEditingController();
  final _localHouseNumberController = TextEditingController();
  final _localStreetController = TextEditingController();
  final _localPhoneController = TextEditingController();
  final _emailController = TextEditingController();
  final _permanentHouseNumberController = TextEditingController();
  final _permanentStreetController = TextEditingController();
  final _permanentVillageController = TextEditingController();
  final _permanentDistrictController = TextEditingController();
  final _permanentNationController = TextEditingController();
  final _permanentCellController = TextEditingController();
  final _passwordController = TextEditingController();
  final _confirmPasswordController = TextEditingController();
  final _designationController = TextEditingController();
  final _departmentController = TextEditingController();

  @override
  void dispose() {
    _nameController.dispose();
    _dobController.dispose();
    _rollNumberController.dispose();
    _hostelNameController.dispose();
    _roomNumberController.dispose();
    _bloodGroupController.dispose();
    _ageController.dispose();
    _sexController.dispose();
    _localHouseNumberController.dispose();
    _localStreetController.dispose();
    _localPhoneController.dispose();
    _emailController.dispose();
    _permanentHouseNumberController.dispose();
    _permanentStreetController.dispose();
    _permanentVillageController.dispose();
    _permanentDistrictController.dispose();
    _permanentNationController.dispose();
    _permanentCellController.dispose();
    _passwordController.dispose();
    _confirmPasswordController.dispose();
    _designationController.dispose();
    _departmentController.dispose();
    super.dispose();
  }

  Future<void> _selectDate(BuildContext context) async {
    final DateTime? picked = await showDatePicker(
      context: context,
      initialDate: DateTime.now(),
      firstDate: DateTime(1900),
      lastDate: DateTime.now(),
    );
    if (picked != null) {
      setState(() {
        _dobController.text = DateFormat('yyyy-MM-dd').format(picked);
      });
    }
  }

  void _submitForm() {
    if (_formKey.currentState!.validate()) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text('${widget.userType} registration submitted successfully!'),
          duration: const Duration(seconds: 2),
        ),
      );
      _formKey.currentState!.reset();
      _nameController.clear();
      _dobController.clear();
      _rollNumberController.clear();
      _hostelNameController.clear();
      _roomNumberController.clear();
      _bloodGroupController.clear();
      _ageController.clear();
      _sexController.clear();
      _localHouseNumberController.clear();
      _localStreetController.clear();
      _localPhoneController.clear();
      _emailController.clear();
      _permanentHouseNumberController.clear();
      _permanentStreetController.clear();
      _permanentVillageController.clear();
      _permanentDistrictController.clear();
      _permanentNationController.clear();
      _permanentCellController.clear();
      _passwordController.clear();
      _confirmPasswordController.clear();
      _designationController.clear();
      _departmentController.clear();
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('${widget.userType} Sign Up'),
        leading: IconButton(
          icon: const Icon(Icons.arrow_back),
          onPressed: () {
            Navigator.pop(context);
          },
        ),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              if (widget.userType == 'Student') ...[
                TextFormField(
                  controller: _nameController,
                  decoration: const InputDecoration(
                    labelText: 'Name',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your name';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _dobController,
                  decoration: const InputDecoration(
                    labelText: 'Date of Birth (YYYY-MM-DD)',
                    border: OutlineInputBorder(),
                  ),
                  readOnly: true,
                  onTap: () => _selectDate(context),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please select your date of birth';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _rollNumberController,
                  decoration: const InputDecoration(
                    labelText: 'Roll Number',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your roll number';
                    }
                    if (!RegExp(r'^[A-Za-z0-9]+$').hasMatch(value)) {
                      return 'Roll number can only contain letters and numbers';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _hostelNameController,
                  decoration: const InputDecoration(
                    labelText: 'Hostel Name',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your hostel name';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _roomNumberController,
                  decoration: const InputDecoration(
                    labelText: 'Room Number',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your room number';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _bloodGroupController,
                  decoration: const InputDecoration(
                    labelText: 'Blood Group',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your blood group';
                    }
                    if (!RegExp(r'^(A|B|AB|O)[+-]$').hasMatch(value)) {
                      return 'Please enter a valid blood group (e.g., A+, B-, AB+, O-)';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _ageController,
                  decoration: const InputDecoration(
                    labelText: 'Age',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.number,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your age';
                    }
                    final age = int.tryParse(value);
                    if (age == null || age < 1 || age > 150) {
                      return 'Please enter a valid age';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _sexController,
                  decoration: const InputDecoration(
                    labelText: 'Sex',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your sex';
                    }
                    if (!['Male', 'Female', 'Other'].contains(value)) {
                      return 'Please enter Male, Female, or Other';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 20),
                const Text(
                  'Local Address',
                  style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _localHouseNumberController,
                  decoration: const InputDecoration(
                    labelText: 'House Number',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter house number';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _localStreetController,
                  decoration: const InputDecoration(
                    labelText: 'Street',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter street';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _localPhoneController,
                  decoration: const InputDecoration(
                    labelText: 'Phone Number',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.phone,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter phone number';
                    }
                    if (!RegExp(r'^\+?[1-9]\d{1,14}$').hasMatch(value)) {
                      return 'Please enter a valid phone number';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _emailController,
                  decoration: const InputDecoration(
                    labelText: 'Email',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.emailAddress,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your email';
                    }
                    if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value)) {
                      return 'Please enter a valid email';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 20),
                const Text(
                  'Permanent Address',
                  style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _permanentHouseNumberController,
                  decoration: const InputDecoration(
                    labelText: 'House Number',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter house number';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _permanentStreetController,
                  decoration: const InputDecoration(
                    labelText: 'Street',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter street';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _permanentVillageController,
                  decoration: const InputDecoration(
                    labelText: 'Village/Town',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter village/town';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _permanentDistrictController,
                  decoration: const InputDecoration(
                    labelText: 'District/State',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter district/state';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _permanentNationController,
                  decoration: const InputDecoration(
                    labelText: 'Nation',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter nation';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _permanentCellController,
                  decoration: const InputDecoration(
                    labelText: 'Cell Number',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.phone,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter cell number';
                    }
                    if (!RegExp(r'^\+?[1-9]\d{1,14}$').hasMatch(value)) {
                      return 'Please enter a valid cell number';
                    }
                    return null;
                  },
                ),
              ] else if (widget.userType == 'Employee') ...[
                TextFormField(
                  controller: _nameController,
                  decoration: const InputDecoration(
                    labelText: 'Name',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your name';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _dobController,
                  decoration: const InputDecoration(
                    labelText: 'Date of Birth (YYYY-MM-DD)',
                    border: OutlineInputBorder(),
                  ),
                  readOnly: true,
                  onTap: () => _selectDate(context),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please select your date of birth';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _designationController,
                  decoration: const InputDecoration(
                    labelText: 'Designation',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your designation';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _departmentController,
                  decoration: const InputDecoration(
                    labelText: 'Department',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your department';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _bloodGroupController,
                  decoration: const InputDecoration(
                    labelText: 'Blood Group',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your blood group';
                    }
                    if (!RegExp(r'^(A|B|AB|O)[+-]$').hasMatch(value)) {
                      return 'Please enter a valid blood group (e.g., A+, B-, AB+, O-)';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _ageController,
                  decoration: const InputDecoration(
                    labelText: 'Age',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.number,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your age';
                    }
                    final age = int.tryParse(value);
                    if (age == null || age < 1 || age > 150) {
                      return 'Please enter a valid age';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _sexController,
                  decoration: const InputDecoration(
                    labelText: 'Sex',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your sex';
                    }
                    if (!['Male', 'Female', 'Other'].contains(value)) {
                      return 'Please enter Male, Female, or Other';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _emailController,
                  decoration: const InputDecoration(
                    labelText: 'Email',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.emailAddress,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your email';
                    }
                    if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value)) {
                      return 'Please enter a valid email';
                    }
                    return null;
                  },
                ),
              ] else ...[
                TextFormField(
                  controller: _nameController,
                  decoration: const InputDecoration(
                    labelText: 'Name',
                    border: OutlineInputBorder(),
                  ),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your name';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _dobController,
                  decoration: const InputDecoration(
                    labelText: 'Date of Birth (YYYY-MM-DD)',
                    border: OutlineInputBorder(),
                  ),
                  readOnly: true,
                  onTap: () => _selectDate(context),
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please select your date of birth';
                    }
                    return null;
                  },
                ),
                const SizedBox(height: 10),
                TextFormField(
                  controller: _emailController,
                  decoration: const InputDecoration(
                    labelText: 'Email',
                    border: OutlineInputBorder(),
                  ),
                  keyboardType: TextInputType.emailAddress,
                  validator: (value) {
                    if (value == null || value.isEmpty) {
                      return 'Please enter your email';
                    }
                    if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value)) {
                      return 'Please enter a valid email';
                    }
                    return null;
                  },
                ),
              ],
              const SizedBox(height: 10),
              TextFormField(
                controller: _passwordController,
                decoration: const InputDecoration(
                  labelText: 'Create Password',
                  border: OutlineInputBorder(),
                ),
                obscureText: true,
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Please enter a password';
                  }
                  if (value.length < 6) {
                    return 'Password must be at least 6 characters';
                  }
                  return null;
                },
              ),
              const SizedBox(height: 10),
              TextFormField(
                controller: _confirmPasswordController,
                decoration: const InputDecoration(
                  labelText: 'Confirm Password',
                  border: OutlineInputBorder(),
                ),
                obscureText: true,
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Please confirm your password';
                  }
                  if (value != _passwordController.text) {
                    return 'Passwords do not match';
                  }
                  return null;
                },
              ),
              const SizedBox(height: 20),
              Row(
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: [
                  ElevatedButton(
                    onPressed: () {
                      Navigator.pop(context);
                    },
                    style: ElevatedButton.styleFrom(
                      padding: const EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                    ),
                    child: const Text('Back', style: TextStyle(fontSize: 18)),
                  ),
                  ElevatedButton(
                    onPressed: _submitForm,
                    style: ElevatedButton.styleFrom(
                      padding: const EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                    ),
                    child: const Text('Submit', style: TextStyle(fontSize: 18)),
                  ),
                ],
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class LoginForm extends StatefulWidget {
  final String userType;

  const LoginForm({super.key, required this.userType});

  @override
  State<LoginForm> createState() => _LoginFormState();
}

class _LoginFormState extends State<LoginForm> {
  final _formKey = GlobalKey<FormState>();
  final _emailController = TextEditingController();
  final _passwordController = TextEditingController();

  @override
  void dispose() {
    _emailController.dispose();
    _passwordController.dispose();
    super.dispose();
  }

  void _submitLogin() {
    if (_formKey.currentState!.validate()) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text('${widget.userType} login successful!'),
          duration: const Duration(seconds: 2),
        ),
      );
      _formKey.currentState!.reset();
      _emailController.clear();
      _passwordController.clear();
      if (widget.userType == 'Student') {
        Navigator.pushReplacement(
          context,
          MaterialPageRoute(
            builder: (context) => const StudentDashboard(),
          ),
        );
      } else if (widget.userType == 'Employee') {
        Navigator.pushReplacement(
          context,
          MaterialPageRoute(
            builder: (context) => const EmployeeDashboard(),
          ),
        );
      } else if (widget.userType == 'Doctor') {
        Navigator.pushReplacement(
          context,
          MaterialPageRoute(
            builder: (context) => const DoctorDashboard(),
          ),
        );
      } else if (widget.userType == 'Reception') {
        Navigator.pushReplacement(
          context,
          MaterialPageRoute(
            builder: (context) => const ReceptionDashboard(),
          ),
        );
      }
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('${widget.userType} Login'),
        leading: IconButton(
          icon: const Icon(Icons.arrow_back),
          onPressed: () {
            Navigator.pop(context);
          },
        ),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: ListView(
            children: [
              TextFormField(
                controller: _emailController,
                decoration: const InputDecoration(
                  labelText: 'Email',
                  border: OutlineInputBorder(),
                ),
                keyboardType: TextInputType.emailAddress,
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Please enter your email';
                  }
                  if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value)) {
                    return 'Please enter a valid email';
                  }
                  return null;
                },
              ),
              const SizedBox(height: 10),
              TextFormField(
                controller: _passwordController,
                decoration: const InputDecoration(
                  labelText: 'Password',
                  border: OutlineInputBorder(),
                ),
                obscureText: true,
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return 'Please enter your password';
                  }
                  return null;
                },
              ),
              const SizedBox(height: 20),
              Row(
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: [
                  ElevatedButton(
                    onPressed: () {
                      Navigator.pop(context);
                    },
                    style: ElevatedButton.styleFrom(
                      padding: const EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                    ),
                    child: const Text('Back', style: TextStyle(fontSize: 18)),
                  ),
                  ElevatedButton(
                    onPressed: _submitLogin,
                    style: ElevatedButton.styleFrom(
                      padding: const EdgeInsets.symmetric(vertical: 15, horizontal: 30),
                    ),
                    child: const Text('Login', style: TextStyle(fontSize: 18)),
                  ),
                ],
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class StudentDashboard extends StatefulWidget {
  const StudentDashboard({super.key});

  @override
  State<StudentDashboard> createState() => _StudentDashboardState();
}

class _StudentDashboardState extends State<StudentDashboard> {
  final List<Doctor> doctors = const [
    Doctor(
      name: 'Dr. G. Radha Rukmini',
      specialty: 'MBBS',
      designation: 'HOD, Senior Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. Ch. Karthik',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. D. Pradeep',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. A. Karthik',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. J. Sowmya',
      specialty: 'MD Pulmonologist (Chest Physician)',
      isRegular: false,
      timing: 'Every Monday and Thursday, 4:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. B. Sandhya Rani',
      specialty: 'Gynecologist',
      isRegular: false,
      timing: 'Every Month 1st and 2nd Tuesday, 4:00 PM to 5:30 PM',
    ),
    Doctor(
      name: 'Dr. Vasavi Reddy',
      specialty: 'Gynecologist',
      isRegular: false,
      timing: 'Every Month 3rd and 4th Tuesday, 3:30 PM to 5:30 PM',
    ),
    Doctor(
      name: 'Dr. V. Hemanth Reddy',
      specialty: 'Ophthalmologist',
      isRegular: false,
      timing: 'Every Month 1st and 3rd Tuesday, 4:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. B. Jagadeesh Babu',
      specialty: 'Psychiatrist',
      isRegular: false,
      timing: 'Every Wednesday, 5:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. Pradeep',
      specialty: 'Paediatrician',
      isRegular: false,
      timing: 'Every Wednesday, 5:00 PM to 7:00 PM',
    ),
    Doctor(
      name: 'Dr. K. Karthik',
      specialty: 'Dental Doctor',
      isRegular: false,
      timing: 'Every Thursday, 3:00 PM to 5:00 PM',
    ),
    Doctor(
      name: 'Dr. G. Vidya Reddy',
      specialty: 'Dermatologist',
      isRegular: false,
      timing: 'Every Friday, 4:00 PM to 5:30 PM',
    ),
  ];

  int _selectedSection = 0;
  final _nameController = TextEditingController();
  final _dobController = TextEditingController();
  final _rollNumberController = TextEditingController();
  final _hostelNameController = TextEditingController();
  final _roomNumberController = TextEditingController();
  final _bloodGroupController = TextEditingController();
  final _ageController = TextEditingController();
  final _sexController = TextEditingController();
  final _localHouseNumberController = TextEditingController();
  final _localStreetController = TextEditingController();
  final _localPhoneController = TextEditingController();
  final _emailController = TextEditingController();
  final _permanentHouseNumberController = TextEditingController();
  final _permanentStreetController = TextEditingController();
  final _permanentVillageController = TextEditingController();
  final _permanentDistrictController = TextEditingController();
  final _permanentNationController = TextEditingController();
  final _permanentCellController = TextEditingController();

  @override
  void initState() {
    super.initState();
    final clinicState = Provider.of<ClinicState>(context, listen: false);
    if (clinicState.doctorAvailability.isEmpty) {
      clinicState.initialize(doctors);
    }
    _nameController.text = 'John Doe';
    _dobController.text = '2000-01-01';
    _rollNumberController.text = 'S12345';
    _hostelNameController.text = 'Hostel A';
    _roomNumberController.text = '101';
    _bloodGroupController.text = 'A+';
    _ageController.text = '25';
    _sexController.text = 'Male';
    _localHouseNumberController.text = '12';
    _localStreetController.text = 'Main Street';
    _localPhoneController.text = '+919876543210';
    _emailController.text = 'john.doe@example.com';
    _permanentHouseNumberController.text = '34';
    _permanentStreetController.text = 'Park Avenue';
    _permanentVillageController.text = 'Village X';
    _permanentDistrictController.text = 'State Y';
    _permanentNationController.text = 'India';
    _permanentCellController.text = '+918765432109';
  }

  void _showSection(int section) {
    setState(() {
      _selectedSection = _selectedSection == section ? 0 : section;
    });
  }

  void _editDetails() {
    showDialog(
        context: context,
        builder: (context) => AlertDialog(
        title: const Text('Edit Details'),
    content: SingleChildScrollView(
    child: Form(
    key: GlobalKey<FormState>(),
    child: Column(
    mainAxisSize: MainAxisSize.min,
    children: [
    TextFormField(
    controller: _nameController,
    decoration: const InputDecoration(labelText: 'Name'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your name';
    return null;
    },
    ),
    TextFormField(
    controller: _dobController,
    decoration: const InputDecoration(labelText: 'Date of Birth (YYYY-MM-DD)'),
    readOnly: true,
    onTap: () async {
    final DateTime? picked = await showDatePicker(
    context: context,
    initialDate: DateTime.now(),
    firstDate: DateTime(1900),
    lastDate: DateTime.now(),
    );
    if (picked != null) {
    setState(() {
    _dobController.text = DateFormat('yyyy-MM-dd').format(picked);
    });
    }
    },
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please select your date of birth';
    return null;
    },
    ),
    TextFormField(
    controller: _rollNumberController,
    decoration: const InputDecoration(labelText: 'Roll Number'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your roll number';
    if (!RegExp(r'^[A-Za-z0-9]+$').hasMatch(value))
    return 'Roll number can only contain letters and numbers';
    return null;
    },
    ),
    TextFormField(
    controller: _hostelNameController,
    decoration: const InputDecoration(labelText: 'Hostel Name'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your hostel name';
    return null;
    },
    ),
    TextFormField(
    controller: _roomNumberController,
    decoration: const InputDecoration(labelText: 'Room Number'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your room number';
    return null;
    },
    ),
    TextFormField(
    controller: _bloodGroupController,
    decoration: const InputDecoration(labelText: 'Blood Group'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your blood group';
    if (!RegExp(r'^(A|B|AB|O)[+-]$').hasMatch(value))
    return 'Please enter a valid blood group (e.g., A+, B-, AB+, O-)';
    return null;
    },
    ),
    TextFormField(
    controller: _ageController,
    decoration: const InputDecoration(labelText: 'Age'),
    keyboardType: TextInputType.number,
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your age';
    final age = int.tryParse(value);
    if (age == null || age < 1 || age > 150) return 'Please enter a valid age';
    return null;
    },
    ),
    TextFormField(
    controller: _sexController,
    decoration: const InputDecoration(labelText: 'Sex'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your sex';
    if (!['Male', 'Female', 'Other'].contains(value))
    return 'Please enter Male, Female, or Other';
    return null;
    },
    ),
    TextFormField(
    controller: _localHouseNumberController,
    decoration: const InputDecoration(labelText: 'Local House Number'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter house number';
    return null;
    },
    ),
    TextFormField(
    controller: _localStreetController,
    decoration: const InputDecoration(labelText: 'Local Street'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter street';
    return null;
    },
    ),
    TextFormField(
    controller: _localPhoneController,
    decoration: const InputDecoration(labelText: 'Local Phone Number'),
    keyboardType: TextInputType.phone,
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter phone number';
    if (!RegExp(r'^\+?[1-9]\d{1,14}$').hasMatch(value))
    return 'Please enter a valid phone number';
    return null;
    },
    ),
    TextFormField(
    controller: _emailController,
    decoration: const InputDecoration(labelText: 'Email'),
    keyboardType: TextInputType.emailAddress,
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter your email';
    if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value))
    return 'Please enter a valid email';
    return null;
    },
    ),
    TextFormField(
    controller: _permanentHouseNumberController,
    decoration: const InputDecoration(labelText: 'Permanent House Number'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter house number';
    return null;
    },
    ),
    TextFormField(
    controller: _permanentStreetController,
    decoration: const InputDecoration(labelText: 'Permanent Street'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter street';
    return null;
    },
    ),
    TextFormField(
    controller: _permanentVillageController,
    decoration: const InputDecoration(labelText: 'Village/Town'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter village/town';
    return null;
    },
    ),
    TextFormField(
    controller: _permanentDistrictController,
    decoration: const InputDecoration(labelText: 'District/State'),
    validator: (value) {
    if (value == null || value.isEmpty) return 'Please enter district/state';
    return null;
    },
    ),
      TextFormField(
        controller: _permanentNationController,
        decoration: const InputDecoration(labelText: 'Nation'),
        validator: (value) {
          if (value == null || value.isEmpty) return 'Please enter nation';
          return null;
        },
      ),
      TextFormField(
        controller: _permanentCellController,
        decoration: const InputDecoration(labelText: 'Permanent Cell Number'),
        keyboardType: TextInputType.phone,
        validator: (value) {
          if (value == null || value.isEmpty) return 'Please enter cell number';
          if (!RegExp(r'^\+?[1-9]\d{1,14}$').hasMatch(value))
            return 'Please enter a valid cell number';
          return null;
        },
      ),
    ],
    ),
    ),
    ),
          actions: [
            TextButton(
              onPressed: () {
                if (Form.of(context).validate()) {
                  Navigator.pop(context);
                  setState(() {});
                }
              },
              child: const Text('Save'),
            ),
          ],
        ),
    );
  }

  @override
  Widget build(BuildContext context) {
    final clinicState = Provider.of<ClinicState>(context);
    final regularDoctors = doctors
        .where((doctor) => doctor.isRegular && clinicState.doctorAvailability[doctor.name] == true)
        .toList();
    final visitingDoctors = doctors
        .where((doctor) => !doctor.isRegular && clinicState.doctorAvailability[doctor.name] == true)
        .toList();

    return Scaffold(
      appBar: AppBar(
        title: const Text('Student Dashboard'),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(1),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('Doctors', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(2),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('About', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
              ],
            ),
            const SizedBox(height: 20),
            if (_selectedSection == 1) ...[
              const Text(
                'Available Doctors',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              Column(
                children: [
                  const Text(
                    'Regular Doctors',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 5),
                  if (regularDoctors.isEmpty)
                    const Text('No regular doctors available.', style: TextStyle(color: Colors.grey))
                  else
                    ...regularDoctors.map((doctor) => Card(
                      margin: const EdgeInsets.symmetric(vertical: 5),
                      child: ListTile(
                        title: Text(doctor.name),
                        subtitle: Column(
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: [
                            Text(doctor.specialty),
                            if (doctor.designation != null) ...[
                              const SizedBox(height: 4),
                              Text(doctor.designation!,
                                  style: const TextStyle(fontSize: 12, color: Colors.grey)),
                            ],
                          ],
                        ),
                        trailing: ElevatedButton(
                          onPressed: () {
                            ScaffoldMessenger.of(context).showSnackBar(
                              SnackBar(
                                content: Text('Appointment booked with ${doctor.name}'),
                                duration: const Duration(seconds: 2),
                              ),
                            );
                          },
                          child: const Text('Book'),
                        ),
                      ),
                    )),
                  const SizedBox(height: 10),
                  const Text(
                    'Visiting Doctors',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 5),
                  if (visitingDoctors.isEmpty)
                    const Text('No visiting doctors available.', style: TextStyle(color: Colors.grey))
                  else
                    ...visitingDoctors.map((doctor) => Card(
                      margin: const EdgeInsets.symmetric(vertical: 5),
                      child: ListTile(
                        title: Text(doctor.name),
                        subtitle: Column(
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: [
                            Text(doctor.specialty),
                            if (doctor.timing != null) ...[
                              const SizedBox(height: 4),
                              Text('Timing: ${doctor.timing}',
                                  style: const TextStyle(fontSize: 12, color: Colors.grey)),
                            ],
                          ],
                        ),
                        trailing: ElevatedButton(
                          onPressed: () {
                            ScaffoldMessenger.of(context).showSnackBar(
                              SnackBar(
                                content: Text('Appointment booked with ${doctor.name}'),
                                duration: const Duration(seconds: 2),
                              ),
                            );
                          },
                          child: const Text('Book'),
                        ),
                      ),
                    )),
                ],
              ),
            ] else if (_selectedSection == 2) ...[
              const Text(
                'About Me',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              Card(
                margin: const EdgeInsets.symmetric(vertical: 5),
                child: Padding(
                  padding: const EdgeInsets.all(10.0),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text('Name: ${_nameController.text}'),
                      Text('Date of Birth: ${_dobController.text}'),
                      Text('Roll Number: ${_rollNumberController.text}'),
                      Text('Hostel Name: ${_hostelNameController.text}'),
                      Text('Room Number: ${_roomNumberController.text}'),
                      Text('Blood Group: ${_bloodGroupController.text}'),
                      Text('Age: ${_ageController.text}'),
                      Text('Sex: ${_sexController.text}'),
                      const SizedBox(height: 10),
                      const Text(
                        'Local Address',
                        style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
                      ),
                      Text('House Number: ${_localHouseNumberController.text}'),
                      Text('Street: ${_localStreetController.text}'),
                      Text('Phone Number: ${_localPhoneController.text}'),
                      Text('Email: ${_emailController.text}'),
                      const SizedBox(height: 10),
                      const Text(
                        'Permanent Address',
                        style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
                      ),
                      Text('House Number: ${_permanentHouseNumberController.text}'),
                      Text('Street: ${_permanentStreetController.text}'),
                      Text('Village/Town: ${_permanentVillageController.text}'),
                      Text('District/State: ${_permanentDistrictController.text}'),
                      Text('Nation: ${_permanentNationController.text}'),
                      Text('Cell Number: ${_permanentCellController.text}'),
                    ],
                  ),
                ),
              ),
              const SizedBox(height: 10),
              Center(
                child: ElevatedButton(
                  onPressed: _editDetails,
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(vertical: 10, horizontal: 20),
                  ),
                  child: const Text('Edit', style: TextStyle(fontSize: 16)),
                ),
              ),
            ],
          ],
        ),
      ),
    );
  }
}

class EmployeeDashboard extends StatefulWidget {
  const EmployeeDashboard({super.key});

  @override
  State<EmployeeDashboard> createState() => _EmployeeDashboardState();
}

class _EmployeeDashboardState extends State<EmployeeDashboard> {
  final List<Doctor> doctors = const [
    Doctor(
      name: 'Dr. G. Radha Rukmini',
      specialty: 'MBBS',
      designation: 'HOD, Senior Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. Ch. Karthik',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. D. Pradeep',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. A. Karthik',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. J. Sowmya',
      specialty: 'MD Pulmonologist (Chest Physician)',
      isRegular: false,
      timing: 'Every Monday and Thursday, 4:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. B. Sandhya Rani',
      specialty: 'Gynecologist',
      isRegular: false,
      timing: 'Every Month 1st and 2nd Tuesday, 4:00 PM to 5:30 PM',
    ),
    Doctor(
      name: 'Dr. Vasavi Reddy',
      specialty: 'Gynecologist',
      isRegular: false,
      timing: 'Every Month 3rd and 4th Tuesday, 3:30 PM to 5:30 PM',
    ),
    Doctor(
      name: 'Dr. V. Hemanth Reddy',
      specialty: 'Ophthalmologist',
      isRegular: false,
      timing: 'Every Month 1st and 3rd Tuesday, 4:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. B. Jagadeesh Babu',
      specialty: 'Psychiatrist',
      isRegular: false,
      timing: 'Every Wednesday, 5:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. Pradeep',
      specialty: 'Paediatrician',
      isRegular: false,
      timing: 'Every Wednesday, 5:00 PM to 7:00 PM',
    ),
    Doctor(
      name: 'Dr. K. Karthik',
      specialty: 'Dental Doctor',
      isRegular: false,
      timing: 'Every Thursday, 3:00 PM to 5:00 PM',
    ),
    Doctor(
      name: 'Dr. G. Vidya Reddy',
      specialty: 'Dermatologist',
      isRegular: false,
      timing: 'Every Friday, 4:00 PM to 5:30 PM',
    ),
  ];

  int _selectedSection = 0;
  final _nameController = TextEditingController();
  final _dobController = TextEditingController();
  final _emailController = TextEditingController();
  final _designationController = TextEditingController();
  final _departmentController = TextEditingController();
  final _bloodGroupController = TextEditingController();
  final _ageController = TextEditingController();
  final _sexController = TextEditingController();

  @override
  void initState() {
    super.initState();
    final clinicState = Provider.of<ClinicState>(context, listen: false);
    if (clinicState.doctorAvailability.isEmpty) {
      clinicState.initialize(doctors);
    }
    _nameController.text = 'Jane Smith';
    _dobController.text = '1985-05-15';
    _emailController.text = 'jane.smith@example.com';
    _designationController.text = 'Professor';
    _departmentController.text = 'Computer Science';
    _bloodGroupController.text = 'B+';
    _ageController.text = '40';
    _sexController.text = 'Female';
  }

  void _showSection(int section) {
    setState(() {
      _selectedSection = _selectedSection == section ? 0 : section;
    });
  }

  void _editDetails() {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Edit Details'),
        content: SingleChildScrollView(
          child: Form(
            key: GlobalKey<FormState>(),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                TextFormField(
                  controller: _nameController,
                  decoration: const InputDecoration(labelText: 'Name'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your name';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _dobController,
                  decoration: const InputDecoration(labelText: 'Date of Birth (YYYY-MM-DD)'),
                  readOnly: true,
                  onTap: () async {
                    final DateTime? picked = await showDatePicker(
                      context: context,
                      initialDate: DateTime.now(),
                      firstDate: DateTime(1900),
                      lastDate: DateTime.now(),
                    );
                    if (picked != null) {
                      setState(() {
                        _dobController.text = DateFormat('yyyy-MM-dd').format(picked);
                      });
                    }
                  },
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please select your date of birth';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _designationController,
                  decoration: const InputDecoration(labelText: 'Designation'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your designation';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _departmentController,
                  decoration: const InputDecoration(labelText: 'Department'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your department';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _bloodGroupController,
                  decoration: const InputDecoration(labelText: 'Blood Group'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your blood group';
                    if (!RegExp(r'^(A|B|AB|O)[+-]$').hasMatch(value))
                      return 'Please enter a valid blood group (e.g., A+, B-, AB+, O-)';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _ageController,
                  decoration: const InputDecoration(labelText: 'Age'),
                  keyboardType: TextInputType.number,
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your age';
                    final age = int.tryParse(value);
                    if (age == null || age < 1 || age > 150) return 'Please enter a valid age';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _sexController,
                  decoration: const InputDecoration(labelText: 'Sex'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your sex';
                    if (!['Male', 'Female', 'Other'].contains(value))
                      return 'Please enter Male, Female, or Other';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _emailController,
                  decoration: const InputDecoration(labelText: 'Email'),
                  keyboardType: TextInputType.emailAddress,
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your email';
                    if (!RegExp(r'^[\w-\.]+@([\w-]+\.)+[\w-]{2,4}$').hasMatch(value))
                      return 'Please enter a valid email';
                    return null;
                  },
                ),
              ],
            ),
          ),
        ),
        actions: [
          TextButton(
            onPressed: () {
              if (Form.of(context).validate()) {
                Navigator.pop(context);
                setState(() {});
              }
            },
            child: const Text('Save'),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    final clinicState = Provider.of<ClinicState>(context);
    final regularDoctors = doctors
        .where((doctor) => doctor.isRegular && clinicState.doctorAvailability[doctor.name] == true)
        .toList();
    final visitingDoctors = doctors
        .where((doctor) => !doctor.isRegular && clinicState.doctorAvailability[doctor.name] == true)
        .toList();

    return Scaffold(
      appBar: AppBar(
        title: const Text('Employee Dashboard'),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(1),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('Doctors', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(2),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('About', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
              ],
            ),
            const SizedBox(height: 20),
            if (_selectedSection == 1) ...[
              const Text(
                'Available Doctors',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              Column(
                children: [
                  const Text(
                    'Regular Doctors',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 5),
                  if (regularDoctors.isEmpty)
                    const Text('No regular doctors available.', style: TextStyle(color: Colors.grey))
                  else
                    ...regularDoctors.map((doctor) => Card(
                      margin: const EdgeInsets.symmetric(vertical: 5),
                      child: ListTile(
                        title: Text(doctor.name),
                        subtitle: Column(
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: [
                            Text(doctor.specialty),
                            if (doctor.designation != null) ...[
                              const SizedBox(height: 4),
                              Text(doctor.designation!,
                                  style: const TextStyle(fontSize: 12, color: Colors.grey)),
                            ],
                          ],
                        ),
                        trailing: ElevatedButton(
                          onPressed: () {
                            ScaffoldMessenger.of(context).showSnackBar(
                              SnackBar(
                                content: Text('Appointment booked with ${doctor.name}'),
                                duration: const Duration(seconds: 2),
                              ),
                            );
                          },
                          child: const Text('Book'),
                        ),
                      ),
                    )),
                  const SizedBox(height: 10),
                  const Text(
                    'Visiting Doctors',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 5),
                  if (visitingDoctors.isEmpty)
                    const Text('No visiting doctors available.', style: TextStyle(color: Colors.grey))
                  else
                    ...visitingDoctors.map((doctor) => Card(
                      margin: const EdgeInsets.symmetric(vertical: 5),
                      child: ListTile(
                        title: Text(doctor.name),
                        subtitle: Column(
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: [
                            Text(doctor.specialty),
                            if (doctor.timing != null) ...[
                              const SizedBox(height: 4),
                              Text('Timing: ${doctor.timing}',
                                  style: const TextStyle(fontSize: 12, color: Colors.grey)),
                            ],
                          ],
                        ),
                        trailing: ElevatedButton(
                          onPressed: () {
                            ScaffoldMessenger.of(context).showSnackBar(
                              SnackBar(
                                content: Text('Appointment booked with ${doctor.name}'),
                                duration: const Duration(seconds: 2),
                              ),
                            );
                          },
                          child: const Text('Book'),
                        ),
                      ),
                    )),
                ],
              ),
            ] else if (_selectedSection == 2) ...[
              const Text(
                'About Me',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              Card(
                margin: const EdgeInsets.symmetric(vertical: 5),
                child: Padding(
                  padding: const EdgeInsets.all(10.0),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text('Name: ${_nameController.text}'),
                      Text('Date of Birth: ${_dobController.text}'),
                      Text('Designation: ${_designationController.text}'),
                      Text('Department: ${_departmentController.text}'),
                      Text('Blood Group: ${_bloodGroupController.text}'),
                      Text('Age: ${_ageController.text}'),
                      Text('Sex: ${_sexController.text}'),
                      Text('Email: ${_emailController.text}'),
                    ],
                  ),
                ),
              ),
              const SizedBox(height: 10),
              Center(
                child: ElevatedButton(
                  onPressed: _editDetails,
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(vertical: 10, horizontal: 20),
                  ),
                  child: const Text('Edit', style: TextStyle(fontSize: 16)),
                ),
              ),
            ],
          ],
        ),
      ),
    );
  }
}

class DoctorDashboard extends StatefulWidget {
  const DoctorDashboard({super.key});

  @override
  State<DoctorDashboard> createState() => _DoctorDashboardState();
}

class _DoctorDashboardState extends State<DoctorDashboard> {
  final String currentDoctor = 'Dr. G. Radha Rukmini';
  final String specialty = 'MBBS';
  final String designation = 'HOD, Senior Medical Officer';
  final bool isRegular = true;
  final String? timing = null;
  final _nameController = TextEditingController();
  final _specialtyController = TextEditingController();
  final _designationController = TextEditingController();
  final _timingController = TextEditingController();

  int _selectedSection = 0;

  @override
  void initState() {
    super.initState();
    _nameController.text = currentDoctor;
    _specialtyController.text = specialty;
    _designationController.text = designation;
    _timingController.text = timing ?? '';
  }

  void _showSection(int section) {
    setState(() {
      _selectedSection = _selectedSection == section ? 0 : section;
    });
  }

  void _editDetails() {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Edit Details'),
        content: SingleChildScrollView(
          child: Form(
            key: GlobalKey<FormState>(),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                TextFormField(
                  controller: _nameController,
                  decoration: const InputDecoration(labelText: 'Name'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your name';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _specialtyController,
                  decoration: const InputDecoration(labelText: 'Specialty'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your specialty';
                    return null;
                  },
                ),
                TextFormField(
                  controller: _designationController,
                  decoration: const InputDecoration(labelText: 'Designation'),
                  validator: (value) {
                    if (value == null || value.isEmpty) return 'Please enter your designation';
                    return null;
                  },
                ),
                if (!isRegular)
                  TextFormField(
                    controller: _timingController,
                    decoration: const InputDecoration(labelText: 'Timing'),
                    validator: (value) {
                      if (value == null || value.isEmpty) return 'Please enter your timing';
                      return null;
                    },
                  ),
              ],
            ),
          ),
        ),
        actions: [
          TextButton(
            onPressed: () {
              if (Form.of(context).validate()) {
                Navigator.pop(context);
                setState(() {});
              }
            },
            child: const Text('Save'),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    final clinicState = Provider.of<ClinicState>(context);
    final appointments = clinicState.doctorAppointments[currentDoctor] ?? [];

    return Scaffold(
      appBar: AppBar(
        title: const Text('Doctor Dashboard'),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(1),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('Appointments', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(2),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('About', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
              ],
            ),
            const SizedBox(height: 20),
            if (_selectedSection == 1) ...[
              const Text(
                'Accepted Appointments',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              if (appointments.isEmpty)
                const Text('No appointments assigned.', style: TextStyle(color: Colors.grey))
              else
                ...appointments.map((appointment) => Card(
                  margin: const EdgeInsets.symmetric(vertical: 5),
                  child: ListTile(
                    title: Text('Patient: ${appointment['patient']}'),
                    subtitle: Text('Doctor: ${appointment['doctor']}'),
                  ),
                )),
            ] else if (_selectedSection == 2) ...[
              const Text(
                'About Me',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              Card(
                margin: const EdgeInsets.symmetric(vertical: 5),
                child: Padding(
                  padding: const EdgeInsets.all(10.0),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text('Name: ${_nameController.text}'),
                      Text('Specialty: ${_specialtyController.text}'),
                      Text('Designation: ${_designationController.text}'),
                      if (_timingController.text.isNotEmpty) Text('Timing: ${_timingController.text}'),
                    ],
                  ),
                ),
              ),
              const SizedBox(height: 10),
              Center(
                child: ElevatedButton(
                  onPressed: _editDetails,
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(vertical: 10, horizontal: 20),
                  ),
                  child: const Text('Edit', style: TextStyle(fontSize: 16)),
                ),
              ),
            ],
          ],
        ),
      ),
    );
  }
}

class ReceptionDashboard extends StatefulWidget {
  const ReceptionDashboard({super.key});

  @override
  State<ReceptionDashboard> createState() => _ReceptionDashboardState();
}

class _ReceptionDashboardState extends State<ReceptionDashboard> {
  final List<Doctor> doctors = const [
    Doctor(
      name: 'Dr. G. Radha Rukmini',
      specialty: 'MBBS',
      designation: 'HOD, Senior Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. Ch. Karthik',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. D. Pradeep',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. A. Karthik',
      specialty: 'MBBS',
      designation: 'Medical Officer',
      isRegular: true,
    ),
    Doctor(
      name: 'Dr. J. Sowmya',
      specialty: 'MD Pulmonologist (Chest Physician)',
      isRegular: false,
      timing: 'Every Monday and Thursday, 4:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. B. Sandhya Rani',
      specialty: 'Gynecologist',
      isRegular: false,
      timing: 'Every Month 1st and 2nd Tuesday, 4:00 PM to 5:30 PM',
    ),
    Doctor(
      name: 'Dr. Vasavi Reddy',
      specialty: 'Gynecologist',
      isRegular: false,
      timing: 'Every Month 3rd and 4th Tuesday, 3:30 PM to 5:30 PM',
    ),
    Doctor(
      name: 'Dr. V. Hemanth Reddy',
      specialty: 'Ophthalmologist',
      isRegular: false,
      timing: 'Every Month 1st and 3rd Tuesday, 4:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. B. Jagadeesh Babu',
      specialty: 'Psychiatrist',
      isRegular: false,
      timing: 'Every Wednesday, 5:00 PM to 6:00 PM',
    ),
    Doctor(
      name: 'Dr. Pradeep',
      specialty: 'Paediatrician',
      isRegular: false,
      timing: 'Every Wednesday, 5:00 PM to 7:00 PM',
    ),
    Doctor(
      name: 'Dr. K. Karthik',
      specialty: 'Dental Doctor',
      isRegular: false,
      timing: 'Every Thursday, 3:00 PM to 5:00 PM',
    ),
    Doctor(
      name: 'Dr. G. Vidya Reddy',
      specialty: 'Dermatologist',
      isRegular: false,
      timing: 'Every Friday, 4:00 PM to 5:30 PM',
    ),
  ];

  int _selectedSection = 0;
  final _patientNameController = TextEditingController();
  final _doctorNameController = TextEditingController();

  @override
  void initState() {
    super.initState();
    final clinicState = Provider.of<ClinicState>(context, listen: false);
    if (clinicState.doctorAvailability.isEmpty) {
      clinicState.initialize(doctors);
    }
  }

  void _showSection(int section) {
    setState(() {
      _selectedSection = _selectedSection == section ? 0 : section;
    });
  }

  void _assignAppointment() {
    showDialog(
      context: context,
      builder: (context) => AlertDialog(
        title: const Text('Assign Appointment'),
        content: Form(
          key: GlobalKey<FormState>(),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              TextFormField(
                controller: _patientNameController,
                decoration: const InputDecoration(labelText: 'Patient Name'),
                validator: (value) {
                  if (value == null || value.isEmpty) return 'Please enter patient name';
                  return null;
                },
              ),
              TextFormField(
                controller: _doctorNameController,
                decoration: const InputDecoration(labelText: 'Doctor Name'),
                validator: (value) {
                  if (value == null || value.isEmpty) return 'Please enter doctor name';
                  if (!doctors.any((doctor) => doctor.name == value)) return 'Doctor not found';
                  return null;
                },
              ),
            ],
          ),
        ),
        actions: [
          TextButton(
            onPressed: () {
              if (Form.of(context).validate()) {
                final clinicState = Provider.of<ClinicState>(context, listen: false);
                clinicState.acceptAppointment(
                  _doctorNameController.text,
                  {
                    'patient': _patientNameController.text,
                    'doctor': _doctorNameController.text,
                  },
                );
                Navigator.pop(context);
                _patientNameController.clear();
                _doctorNameController.clear();
                ScaffoldMessenger.of(context).showSnackBar(
                  const SnackBar(
                    content: Text('Appointment assigned successfully!'),
                    duration: Duration(seconds: 2),
                  ),
                );
              }
            },
            child: const Text('Assign'),
          ),
        ],
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    final clinicState = Provider.of<ClinicState>(context);
    final regularDoctors = doctors
        .where((doctor) => doctor.isRegular)
        .toList();
    final visitingDoctors = doctors
        .where((doctor) => !doctor.isRegular)
        .toList();

    return Scaffold(
      appBar: AppBar(
        title: const Text('Reception Dashboard'),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(1),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('Doctors', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
                Expanded(
                  child: Container(
                    margin: const EdgeInsets.symmetric(horizontal: 5),
                    child: ElevatedButton(
                      onPressed: () => _showSection(2),
                      style: ElevatedButton.styleFrom(
                        padding: const EdgeInsets.symmetric(vertical: 20),
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.circular(10),
                        ),
                      ),
                      child: const Text('Assign', style: TextStyle(fontSize: 18)),
                    ),
                  ),
                ),
              ],
            ),
            const SizedBox(height: 20),
            if (_selectedSection == 1) ...[
              const Text(
                'Doctors List',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              Column(
                children: [
                  const Text(
                    'Regular Doctors',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 5),
                  if (regularDoctors.isEmpty)
                    const Text('No regular doctors available.', style: TextStyle(color: Colors.grey))
                  else
                    ...regularDoctors.map((doctor) => Card(
                      margin: const EdgeInsets.symmetric(vertical: 5),
                      child: ListTile(
                        title: Text(doctor.name),
                        subtitle: Column(
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: [
                            Text(doctor.specialty),
                            if (doctor.designation != null) ...[
                              const SizedBox(height: 4),
                              Text(doctor.designation!,
                                  style: const TextStyle(fontSize: 12, color: Colors.grey)),
                            ],
                          ],
                        ),
                        trailing: Switch(
                          value: clinicState.doctorAvailability[doctor.name] ?? false,
                          onChanged: (value) {
                            clinicState.toggleAvailability(doctor.name, value);
                          },
                        ),
                      ),
                    )),
                  const SizedBox(height: 10),
                  const Text(
                    'Visiting Doctors',
                    style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold),
                  ),
                  const SizedBox(height: 5),
                  if (visitingDoctors.isEmpty)
                    const Text('No visiting doctors available.', style: TextStyle(color: Colors.grey))
                  else
                    ...visitingDoctors.map((doctor) => Card(
                      margin: const EdgeInsets.symmetric(vertical: 5),
                      child: ListTile(
                        title: Text(doctor.name),
                        subtitle: Column(
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: [
                            Text(doctor.specialty),
                            if (doctor.timing != null) ...[
                              const SizedBox(height: 4),
                              Text('Timing: ${doctor.timing}',
                                  style: const TextStyle(fontSize: 12, color: Colors.grey)),
                            ],
                          ],
                        ),
                        trailing: Switch(
                          value: clinicState.doctorAvailability[doctor.name] ?? false,
                          onChanged: (value) {
                            clinicState.toggleAvailability(doctor.name, value);
                          },
                        ),
                      ),
                    )),
                ],
              ),
            ] else if (_selectedSection == 2) ...[
              const Text(
                'Assign Appointment',
                style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 10),
              Center(
                child: ElevatedButton(
                  onPressed: _assignAppointment,
                  style: ElevatedButton.styleFrom(
                    padding: const EdgeInsets.symmetric(vertical: 10, horizontal: 20),
                  ),
                  child: const Text('Assign Appointment', style: TextStyle(fontSize: 16)),
                ),
              ),
            ],
          ],
        ),
      ),
    );
  }
}
