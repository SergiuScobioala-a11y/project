import json
from typing import List, Dict, Union


# Definirea clasei Angajat
class Employee:
    def __init__(self, cnp: str, name: str, surname: str, age: int,
                 salary: float, department: str, seniority: str):
        """
        Initializeaza obiectul angajat cu urmatoarele optiuni.

	1) Adaugare angajat
	2) Cautare angajat (dupa CNP)
	3) Modificare date angajat (dupa CNP)
	4) Stergere angajat (dupa CNP)
	5) Afisare angajati
	6) Calcul cost total salarii companie
	7) Calcul cost total salarii departament
	8) Calcul fluturas salar angajat (dupa CNP) (CAS - 10% din brut, CASS - 25% din brut, Impozit - 10% din ce a ramas)
	9) Afisarea angajatilor pe baza senioritatii
	10) Afisarea angajatilor pe baza departamentului(junior, mid, senior)
	11) Iesire 
        """
        self.set_cnp(cnp)
        self.name = name
        self.surname = surname
        self.set_age(age)
        self.set_salary(salary)
        self.department = department
        self.seniority = seniority

    def set_cnp(self, cnp: str):
        if len(cnp) != 13 or not cnp.isdigit():
            raise ValueError("CNP trebue sa fie de 13 cifre.")
        self.cnp = cnp

    def set_age(self, age: int):
        if age < 18:
            raise ValueError("Virsta trebue sa fie mai mare de 18 ani.")
        self.age = age

    def set_salary(self, salary: float):
        if salary < 4050:  # Assume 4050 is the minimum wage
            raise ValueError("Salariu trebue sa fie mai mare de 4050.")
        self.salary = salary

    def to_dict(self) -> Dict[str, Union[str, int, float]]:
        """Punem angajatii intr-un dictionar."""
        return {
            "cnp": self.cnp,
            "name": self.name,
            "surname": self.surname,
            "age": self.age,
            "salary": self.salary,
            "department": self.department,
            "seniority": self.seniority,
        }


# Funcții pentru gestionarea angajaților
def load_employees(filename: str) -> List[Employee]:
    """Luam angajatii din JSON file."""
    try:
        with open(filename, 'r') as file:
            data = json.load(file)
            return [Employee(**emp) for emp in data]  
    except FileNotFoundError:
        return []  # Return o lista goala daca nu gasim
    except json.JSONDecodeError:
        print("Error, scrieti datele din fisier.")
        return []


def save_employees(employees: List[Employee], filename: str):
    """Salvarea datelod in JSON file."""
    with open(filename, 'w') as file:
        json.dump([emp.to_dict() for emp in employees], file, indent=4)


def add_employee(employees: List[Employee]):
    """Adauga angajati."""
    try:
        cnp = input("Introduceti  CNP: ")
        name = input("introduceti numele: ")
        surname = input("Itrobuceti prenumele: ")
        age = int(input("Introduceti anii: "))
        salary = float(input("Introduceti salariul: "))
        department = input("Introduceti departamentul: ")
        seniority = input("Introduceti nivelul(junior, mid, senior): ")

        new_employee = Employee(cnp, name, surname, age, salary, department, seniority)
        employees.append(new_employee)
        print("Angajat adaugat cu succes!")
    except ValueError as ve:
        print(f"Error: {ve}")


def search_employee(employees: List[Employee], cnp: str):
    """Cautati angajati dupa CNP."""
    for emp in employees:
        if emp.cnp == cnp:
            print(f"Angajat gasit: {emp.name} {emp.surname}")
            return emp
    print("Angajatul nu este gasit.")
    return None


def modify_employee(employees: List[Employee], cnp: str):
    """Modificarea angajatilor care exista, dupa CNP."""
    employee = search_employee(employees, cnp)
    if employee:
        try:
            age = int(input("Anii noi: "))
            salary = float(input("Salariu nou: "))
            employee.set_age(age)
            employee.set_salary(salary)
            print("Datele sunt salvate cu succes.")
        except ValueError as ve:
            print(f"Error: {ve}")


def delete_employee(employees: List[Employee], cnp: str):
    """Stergerea angajatilor dupa CNP."""
    for i, emp in enumerate(employees):
        if emp.cnp == cnp:
            del employees[i]
            print("Angajat sters cu succes.")
            return
    print("Angajat negasit.")


def display_employees(employees: List[Employee]):
    """Afisarea a tuturor angajatilor."""
    print("Lista de angajati:")
    for emp in employees:
        print(f"{emp.name} {emp.surname}, CNP: {emp.cnp}, Virsta: {emp.age}, Salariu: {emp.salary}, "
              f"Departmentul: {emp.department}, Nivelul: {emp.seniority}")


def total_salary_cost(employees: List[Employee]) -> float:
    """Calculează costul total al salariilor pentru companie."""
    return sum(emp.salary for emp in employees)


def total_salary_cost_by_department(employees: List[Employee], department: str) -> float:
    """Calculează costul total al salariilor pentru un departament specific.."""
    return sum(emp.salary for emp in employees if emp.department == department)


def salary_slip(cnp: str, employees: List[Employee]) -> None:
    """Calculeaza salariu total a unui angajat."""
    employee = search_employee(employees, cnp)
    if employee:
        brut = employee.salary
        cas = 0.10 * brut
        cass = 0.25 * brut
        impozit = 0.10 * (brut - (cas + cass))
        print(f"Salariul total al angajatului {employee.name} {employee.surname}:")
        print(f"Brut Salariu: {brut}")
        print(f"CAS: {cas}")
        print(f"CASS: {cass}")
        print(f"Taxe: {impozit}")
        print(f"Net Salariu: {brut - (cas + cass + impozit)}")


def display_by_seniority(employees: List[Employee], seniority: str):
    """Afișează angajații pe baza de vechime."""
    filtered_employees = [emp for emp in employees if emp.seniority == seniority]
    if filtered_employees:
        print(f"Angajati cu vechime {seniority}:")
        for emp in filtered_employees:
            print(f"{emp.name} {emp.surname}, CNP: {emp.cnp}")
    else:
        print(f"Nu sa gasit angajati cu vechime {seniority}.")


def display_by_department(employees: List[Employee], department: str):
    """Afișează angajații pe baza departamentului."""
    filtered_employees = [emp for emp in employees if emp.department == department]
    if filtered_employees:
        print(f"Angajați în departament {department}:")
        for emp in filtered_employees:
            print(f"{emp.name} {emp.surname}, CNP: {emp.cnp}")
    else:
        print(f"Nu a fost gasiti angajati in departament {department}.")


def main_menu():
    """Meniul de generare a angajatilor."""
    employees = load_employees('employees.json')
    while True:
        print("\n1. Adauga angajati. ")
        print("2. Cautare dupa CNP")
        print("3. Modificare dupa CNP")
        print("4. Stergerea angajatilor dupa CNP")
        print("5. Afisarea angajatilor")
        print("6. Calculeaza costul total al salariilor pentru companie")
        print("7. Calculeaza costul total al salariilor pentru departament.")
        print("8. Calculeaza fluturasul de salariu pentru angajat după CNP")
        print("9. Afisearea angajatilor dupa vechime")
        print("10. Afisarea angajatilor dupa departamente")
        print("11. Exit")
        
        choice = input("Alege o optiune: ")

        if choice == '1':
            add_employee(employees)
        elif choice == '2':
            cnp = input("Scrie CNP: ")
            search_employee(employees, cnp)
        elif choice == '3':
            cnp = input("Scrie CNP pentru modificare: ")
            modify_employee(employees, cnp)
        elif choice == '4':
            cnp = input("Scrie CNP pentru al sterge: ")
            delete_employee(employees, cnp)
        elif choice == '5':
            display_employees(employees)
        elif choice == '6':
            total_cost = total_salary_cost(employees)
            print(f"Costul total al salariilor pentru companie: {total_cost}")
        elif choice == '7':
            department = input("Introduceti departamentul: ")
            total_cost = total_salary_cost_by_department(employees, department)
            print(f"Costrul total al salariilor pe departamente {department}: {total_cost}")
        elif choice == '8':
            cnp = input("Introduceti CNp pentru fluturasul de salariu: ")
            salary_slip(cnp, employees)
        elif choice == '9':
            seniority = input("Introduceti vechimea (junior, mid, senior): ")
            display_by_seniority(employees, seniority)
        elif choice == '10':
            department = input("Introduceti departamentul: ")
            display_by_department(employees, department)
        elif choice == '11':
            save_employees(employees, 'employees.json')
            print("EXIT din program.")
            break
        else:
            print("Optiune invalida. Incercati din nou.")


if __name__ == "__main__":
    main_menu()
