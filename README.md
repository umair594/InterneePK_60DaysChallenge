# InterneePK_60DaysChallenge
This repository contains the projects I completed during my 60 Days Virtual Internship with Internee.pk. It showcases hands-on work in data analysis, automation, machine learning, and optimization.

Task_03: 
ATS Resume Building screening Automatio :
Source Code :
import tkinter as tk
from tkinter import ttk, filedialog, messagebox, scrolledtext
import PyPDF2
import re
from collections import Counter

class ResumeScreeningApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Resume Screening Automation")
        self.root.geometry("1000x700")
        self.root.configure(bg="#F4F6F8")  # clean light gray background

        # Color palette
        self.bg_color = "#F4F6F8"
        self.primary_color = "#2C3E50"  # dark blue-gray
        self.secondary_color = "#1ABC9C"  # teal
        self.accent_color = "#3498DB"  # blue accent
        self.text_color = "#2C3E50"
        self.light_text = "#ECF0F1"

        # Font
        self.font_main = ("Segoe UI", 12)
        self.font_bold = ("Segoe UI", 12, "bold")
        self.font_title = ("Segoe UI", 26, "bold")

        # ttk style configuration
        style = ttk.Style()
        style.theme_use("clam")
        style.configure("TButton",
                        font=self.font_bold,
                        padding=10,
                        background=self.secondary_color,
                        foreground="white",
                        borderwidth=0)
        style.map("TButton",
                  background=[("active", "#16A085")],
                  relief=[("pressed", "groove")])

        # Sidebar Frame
        self.sidebar = tk.Frame(root, bg=self.primary_color, width=220)
        self.sidebar.pack(side=tk.LEFT, fill=tk.Y)

        self.sidebar_title = tk.Label(
            self.sidebar, text="MENU", font=("Segoe UI", 16, "bold"),
            bg=self.primary_color, fg=self.light_text
        )
        self.sidebar_title.pack(pady=20)

        # Sidebar Buttons
        self.create_sidebar_button("🏠 Home", self.show_home)
        self.create_sidebar_button("📂 Upload Resume", self.show_upload)
        self.create_sidebar_button("🔍 Screen Resume", self.show_screen)
        self.create_sidebar_button("🚀 Publish Project", self.show_publish)

        # Main content area
        self.main_frame = tk.Frame(root, bg=self.bg_color)
        self.main_frame.pack(side=tk.RIGHT, fill=tk.BOTH, expand=True)

        # --- Home Page ---
        self.home_page = tk.Frame(self.main_frame, bg=self.bg_color)
        self.home_label = tk.Label(
            self.home_page,
            text="Welcome to Resume Screening Automation\n\nA Smart Tool for Modern Recruitment",
            font=self.font_title, bg=self.bg_color, fg=self.text_color, justify=tk.CENTER
        )
        self.home_label.pack(pady=100)

        # --- Upload Page ---
        self.upload_page = tk.Frame(self.main_frame, bg=self.bg_color)
        self.upload_label = tk.Label(self.upload_page, text="Upload Your Resume",
                                     font=("Segoe UI", 20, "bold"), bg=self.bg_color, fg=self.text_color)
        self.upload_label.pack(pady=30)
        self.upload_btn = ttk.Button(self.upload_page, text="Select File (PDF/TXT)", command=self.upload_resume)
        self.upload_btn.pack(pady=20)
        self.upload_status = tk.Label(self.upload_page, text="", font=self.font_main, bg=self.bg_color,
                                      fg=self.text_color)
        self.upload_status.pack(pady=10)
        self.flower_canvas_upload = tk.Canvas(self.upload_page, width=200, height=100, bg=self.bg_color,
                                              highlightthickness=0)
        self.flower_canvas_upload.pack()

        # --- Screen Page ---
        self.screen_page = tk.Frame(self.main_frame, bg=self.bg_color)
        self.req_label = tk.Label(
            self.screen_page,
            text="Enter Job Requirements (keywords separated by commas):",
            font=self.font_main, bg=self.bg_color, fg=self.text_color)
        self.req_label.pack(pady=20)
        self.req_entry = ttk.Entry(self.screen_page, width=60, font=self.font_main)
        self.req_entry.pack(pady=5)
        self.screen_btn = ttk.Button(self.screen_page, text="Analyze Resume", command=self.screen_resume)
        self.screen_btn.pack(pady=20)
        self.result_label = tk.Label(self.screen_page, text="", font=("Segoe UI", 18, "bold"), bg=self.bg_color)
        self.result_label.pack(pady=20)
        self.flower_canvas = tk.Canvas(self.screen_page, width=200, height=100, bg=self.bg_color, highlightthickness=0)
        self.flower_canvas.pack()

        # --- Publish Page ---
        self.publish_page = tk.Frame(self.main_frame, bg=self.bg_color)
        self.publish_label = tk.Label(self.publish_page, text="Share Your Project for Extra Points",
                                      font=("Segoe UI", 20, "bold"), bg=self.bg_color, fg=self.text_color)
        self.publish_label.pack(pady=30)
        self.publish_text = scrolledtext.ScrolledText(self.publish_page, width=60, height=8, font=self.font_main)
        self.publish_text.pack(pady=10)
        self.publish_btn = ttk.Button(self.publish_page, text="Publish & Earn Points", command=self.publish_project)
        self.publish_btn.pack(pady=15)
        self.points_label = tk.Label(self.publish_page, text="Extra Points: 0", font=self.font_main,
                                     bg=self.bg_color, fg=self.text_color)
        self.points_label.pack(pady=10)

        # Initialize
        self.resume_text = ""
        self.extra_points = 0
        self.current_page = None
        self.show_home()

    # Sidebar button creator
    def create_sidebar_button(self, text, command):
        btn = tk.Button(self.sidebar, text=text, font=self.font_bold,
                        bg=self.secondary_color, fg="white", bd=0,
                        relief="flat", activebackground="#16A085",
                        cursor="hand2", command=command)
        btn.pack(pady=10, padx=20, fill=tk.X)

    # Page switching
    def hide_all_pages(self):
        for page in [self.home_page, self.upload_page, self.screen_page, self.publish_page]:
            page.pack_forget()

    def show_home(self):
        self.hide_all_pages()
        self.home_page.pack(fill=tk.BOTH, expand=True)
        self.current_page = "home"

    def show_upload(self):
        self.hide_all_pages()
        self.upload_page.pack(fill=tk.BOTH, expand=True)
        self.current_page = "upload"

    def show_screen(self):
        self.hide_all_pages()
        self.screen_page.pack(fill=tk.BOTH, expand=True)
        self.current_page = "screen"

    def show_publish(self):
        self.hide_all_pages()
        self.publish_page.pack(fill=tk.BOTH, expand=True)
        self.current_page = "publish"

    # Functional parts
    def upload_resume(self):
        file_path = filedialog.askopenfilename(filetypes=[("PDF files", "*.pdf"), ("Text files", "*.txt")])
        if file_path:
            if file_path.endswith('.pdf'):
                self.resume_text = self.extract_pdf_text(file_path)
            else:
                with open(file_path, 'r', encoding='utf-8', errors='ignore') as file:
                    self.resume_text = file.read()
            self.upload_status.config(text="Resume uploaded successfully!", fg="#27AE60")
            self.draw_flowers_upload()
            messagebox.showinfo("Success", "Resume uploaded successfully!")

    def extract_pdf_text(self, file_path):
        text = ""
        with open(file_path, 'rb') as file:
            pdf_reader = PyPDF2.PdfReader(file)
            for page in pdf_reader.pages:
                text += page.extract_text() or ""
        return text

    def screen_resume(self):
        if not self.resume_text:
            messagebox.showerror("Error", "Please upload a resume first.")
            return

        requirements = [r.strip().lower() for r in self.req_entry.get().split(',') if r.strip()]
        if not requirements:
            messagebox.showerror("Error", "Please enter job requirements.")
            return

        resume_words = re.findall(r'\b\w+\b', self.resume_text.lower())
        resume_counter = Counter(resume_words)
        matches = sum(1 for req in requirements if req in resume_counter)
        percentage = (matches / len(requirements)) * 100

        if percentage >= 50:
            self.result_label.config(text=f"🎉 Eligible ({percentage:.2f}%)", fg="#27AE60")
            self.draw_flowers()
        else:
            self.result_label.config(text=f"❌ Not Eligible ({percentage:.2f}%)", fg="#E74C3C")
            self.flower_canvas.delete("all")

    def draw_flowers_upload(self):
        self.flower_canvas_upload.delete("all")
        self.flower_canvas_upload.create_text(50, 40, text="🌸", font=("Arial", 30))
        self.flower_canvas_upload.create_text(100, 60, text="🌼", font=("Arial", 30))
        self.flower_canvas_upload.create_text(150, 40, text="🌺", font=("Arial", 30))

    def draw_flowers(self):
        self.flower_canvas.delete("all")
        self.flower_canvas.create_text(50, 40, text="🌸", font=("Arial", 30))
        self.flower_canvas.create_text(100, 60, text="🌼", font=("Arial", 30))
        self.flower_canvas.create_text(150, 40, text="🌺", font=("Arial", 30))

    def publish_project(self):
        project_desc = self.publish_text.get("1.0", tk.END).strip()
        if project_desc:
            self.extra_points += 15
            self.points_label.config(text=f"Extra Points: {self.extra_points}")
            messagebox.showinfo("Success", f"Project published! You earned 15 extra points. Total: {self.extra_points}")
            self.publish_text.delete("1.0", tk.END)
        else:
            messagebox.showerror("Error", "Please enter a project description.")

if __name__ == "__main__":
    root = tk.Tk()
    app = ResumeScreeningApp(root)
    root.mainloop()

# Run this code on Python 

Task_04:
Competitor Benchmarking Dashboard-link

Copy this link and paste on Google searching which this open a complete Dashboard which i create on thebricks.com/...

https://app.thebricks.com/file/ed499e9b-bb1a-48ab-9290-67ee1fab82af

End....

Task_05: 
Introduction your task 

Click the Linked link and see it in the linkedin account
https://www.linkedin.com/posts/muhammad-umair-614955309_interneepk-virtualinternship-internshipexperience-activity-7410748899029684224-i5OE?utm_source=share&utm_medium=member_desktop&rcm=ACoAAE6g9jABSo8xp3Kgl8wC4Gl7qdNChJq30Z4

Task_06:

COmplete app which i created in Lovable.app link:

https://try-and-tune.lovable.app

Click this link and get my idea  sorry i have no access some links and
websites and tools because of my small budget.. Therefore i cant access some tools to create A/B Testing ....
inshallah you will impress my projects which i completed in 60 days ...
I am new in Data Science Field... and i will win inshallah in my futrure...


