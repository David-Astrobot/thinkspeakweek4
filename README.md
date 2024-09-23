using Microsoft.SqlServer.Server;
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.IO.Ports;
using System.Linq;
using System.Net;
using System.Reflection.Emit;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
using static System.Windows.Forms.VisualStyles.VisualStyleElement;

namespace WindowsFormsApp1
{
    public partial class Form1 : Form
    {
        int startflag = 0;
        int flag_sensor;
        string RxString;
        string temp = "30";
        // char charb = 'B';

        public Form1()
        {
            InitializeComponent();
        }

        private void Serial_start_Click(object sender, EventArgs e)
        {
            COM18.PortName = "COM18";
            COM18.BaudRate = 115200;

            COM18.Open();
            if (COM18.IsOpen)
            {

                textBox1.ReadOnly = false;
            }
        }

        private void Serial_stop_Click(object sender, EventArgs e)
        {
            COM18.Close();

            textBox1.ReadOnly = true;

        }

        private void Read_In_TS_Click(object sender, EventArgs e)
        {
            WebClient client = new WebClient();
            label1.Text = client.DownloadString("https://api.thingspeak.com/channels/2598078/feeds.json?results=2");

        }
        private void Form1_Load(object sender, EventArgs e)
        {
            if (COM18.IsOpen)
                COM18.Close();

            COM18.PortName = "COM18";
            COM18.BaudRate = 115200;

        }

        private void label1_Click(object sender, EventArgs e)
        {
        }


        private void timer1_Tick(object sender, EventArgs e)
        {
            if (!string.Equals(textBox1.Text, ""))
            {
                if (COM18.IsOpen) COM18.Close();
                try
                {
                    if (RxString[0] == 'B')
                    {
                        flag_sensor = 10;
                    }
                    const string WRITEKEY = "XB1062PLCA59U8FX";// F0252SK1A7O3D2J9"; ////use your channel API keys
                    string strUpdateBase = "http://api.thingspeak.com/update";
                    string strUpdateURI = strUpdateBase + "?key=" + WRITEKEY;
                    string strField1 = textBox1.Text;
                    HttpWebRequest ThingsSpeakReq;
                    HttpWebResponse ThingsSpeakResp;
                    strUpdateURI += "&field4=" + strField1;

                    if (flag_sensor == 10)
                    {
                        strUpdateURI += "&field4=" + strField1;
                        //strUpdateURI += "&field1=" + temp;
                        flag_sensor = 10;
                    }

                    ThingsSpeakReq = (HttpWebRequest)WebRequest.Create

(strUpdateURI);

                    ThingsSpeakResp = (HttpWebResponse)

ThingsSpeakReq.GetResponse();
                    ThingsSpeakResp.Close();

                    if (!(string.Equals(ThingsSpeakResp.StatusDescription, "OK")))
                    {
                        Exception exData = new Exception

(ThingsSpeakResp.StatusDescription);
                        throw exData;
                    }

                }
                catch (Exception ex)
                {

                }
                textBox1.Text = "";
                COM18.Open();
            }

        }

        private void textBox1_TextChanged(object sender, EventArgs e)
        {

        }

        private void SerialPort1_DataReceived(object sender, System.IO.Ports.SerialDataReceivedEventArgs e)
        {
            Console.WriteLine("Data Received");
            RxString = COM18.ReadExisting();
            temp = RxString;
            this.Invoke(new EventHandler(label1_Click));

        }

        private void label1_Click_1(object sender, EventArgs e)
        {

        }

        private void Start_Click(object sender, EventArgs e)
        {

        }

        private void textBox1_TextChanged_1(object sender, EventArgs e)
        {

        }

        private void Stop_Click(object sender, EventArgs e)
        {

        }
    }
}
