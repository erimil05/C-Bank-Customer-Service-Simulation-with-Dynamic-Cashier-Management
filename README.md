# C-Bank-Customer-Service-Simulation-with-Dynamic-Cashier-Management
Developed a C++ simulation of a bank customer service system using OOP. Managed dynamic opening and closing of cashiers based on customer load, priority-based service, and cashier workload limits. Implemented classes for Bank and Cashier with features for serving, queue management, and resource optimization.

#include <iostream>
using namespace std;

// statheres prosomoiwshs
int K, M, N, L;

// klash tameio
class Cashier {
    bool open_flag;
    bool serving_flag;
    int customers_served;
public:
    Cashier() : open_flag(false), serving_flag(false), customers_served(0) {}

    void open() {
        open_flag = true;
        customers_served = 0;
        cout << "anoixto tameio" << endl;
    }

    void close() {
        open_flag = false;
        cout << "kleisto tameio" << endl;
    }

    void serve() {
        serving_flag = true;
    }

    void free() {
        serving_flag = false;
        customers_served++;
        if (customers_served == L) {
            cout << "to tameio exei yperfotothei kai kleinei" << endl;
            close();
        }
    }

    bool is_open() const {
        return open_flag;
    }

    bool is_free() const {
        return open_flag && !serving_flag;
    }
};

// klash trapeza
class Bank {
    int last_customer;
    int curr_serving;
    Cashier cashiers[5];
    int last_served_cashier; // kyklikh epilogh

public:
    Bank() : last_customer(1), curr_serving(1), last_served_cashier(-1) {
        cashiers[0].open(); // anoigei to prwto tameio
    }

    void enter_customer() {
        int waiting = last_customer - curr_serving;
        int open_cashiers = count_open_cashiers();
        if (waiting > open_cashiers * K) {
            // an den ftanoun ta tameia anoigei neo
            for (int i = 0; i < 5; i++) {
                if (!cashiers[i].is_open()) {
                    cashiers[i].open();
                    break;
                }
            }
        }
        if (waiting <= count_open_cashiers() * K) {
            cout << "neos pelaths, arithmos protereothtas: " << last_customer << endl;
            last_customer++;
        } else {
            cout << "adinamia ejyphrethshs" << endl;
        }
    }

    void serve_customer() {
        // kyklikh anazhthsh gia epomeno eleuthero tameio
        for (int i = 1; i <= 5; i++) {
            int idx = (last_served_cashier + i) % 5;
            if (cashiers[idx].is_free()) {
                cashiers[idx].serve();
                cout << "pelaths noumero: " << curr_serving << " ejyphrethte apo to tamio noumero: " << idx + 1 << endl;
                cashiers[idx].free();
                curr_serving++;
                last_served_cashier = idx;
                return;
            }
        }
        cout << "kanena diathesimo tameio" << endl;
    }

    void exit_customer() {
        int open_cashiers = count_open_cashiers();
        int waiting = last_customer - curr_serving;
        if (waiting <= (open_cashiers - 1) * K) {
            // an periseyei tameio kleinei 1
            for (int i = 0; i < 5; i++) {
                if (cashiers[i].is_open()) {
                    cashiers[i].close();
                    break;
                }
            }
        }
    }

    int count_open_cashiers() const {
        int count = 0;
        for (int i = 0; i < 5; i++) {
            if (cashiers[i].is_open()) count++;
        }
        return count;
    }

    int waiting_customers() const {
        return last_customer - curr_serving;
    }
};

int main() {
    // parametroi apo ton xrhsth
    cout << "Enter K, M, N, L: ";
    cin >> K >> M >> N >> L;

    Bank bank;

    // M gyroi eisodoy pelatwn
    for (int i = 0; i < M; i++) {
        for (int j = 0; j < N; j++) {
            bank.enter_customer();
        }
        // prospatheia ejyphrethshs pelatwn
        for (int j = 0; j < N; j++) {
            bank.serve_customer();
            bank.exit_customer();
        }
        cout << "anamonh pelatwn: " << bank.waiting_customers() << endl;
    }

    // ejyphrethsh ypoloipwn pelatwn
    while (bank.waiting_customers() > 0) {
        bank.serve_customer();
        bank.exit_customer();
    }
    return 0;
}
