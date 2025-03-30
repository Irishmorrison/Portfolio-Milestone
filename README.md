# Portfolio-Milestone
Portfolio Milestone Prodject 

Here is the source code for my portfolio prodject.

#include <thread>
#include <mutex>
#include <condition_variable>
#include <chrono>

std::mutex mtx;
std::condition_variable cv;
bool counting_done = false;

void count_up() {
    for (int i = 1; i <= 20; ++i) {
        std::this_thread::sleep_for(std::chrono::milliseconds(100)); // Simulate work
        std::cout << "Counting Up: " << i << std::endl;
    }
    {
        std::lock_guard<std::mutex> lock(mtx);
        counting_done = true;
    }
    cv.notify_one(); // Notify that counting is done
}

void count_down() {
    std::unique_lock<std::mutex> lock(mtx);
    cv.wait(lock, [] { return counting_done; }); // Wait until notified

    for (int i = 20; i >= 0; --i) {
        std::this_thread::sleep_for(std::chrono::milliseconds(100)); // Simulate work
        std::cout << "Counting Down: " << i << std::endl;
    }
}

int main() {
    std::thread t1(count_up);
    std::thread t2(count_down);

    t1.join(); // Wait for count_up to finish
    t2.join(); // Wait for count_down to finish

    return 0;
}

